### 上传文件至oss 基于element-ui 
```javascript 1.8
<template>
        <el-upload
            :multiple="false"
            class="dz-upload-photo upload-demo "
            :show-file-list="false"
            list-type="picture-card"
            :action="uploadUrl"
            :on-preview="handlePreview"
            :before-upload="beforeUpload"
            :on-remove = "removeUpload"
            :style="{width:width,height: height}"
            :drag="dragble"
            :disabled="disabled"
            :http-request="Upload">
            <el-button v-if="shape === 'button'" type="primary" icon="el-icon-plus">添加文件</el-button>
            <div class="card" v-if="shape === 'card'">
                <i class="el-icon-upload"></i>
                <p class="tip">{{tip}}</p>
            </div>
        </el-upload>
</template>

<script>
    /**
     * 单张图片上传组件
     * 1.父组件监听upload-success 事件获取最新文件
     * 2.父组件监听progress-change 获取进度条百分比
     * 3.图片初始值：defaultImg
     * shape  默认card 可选button circle
     * 删除有一个问题 并没有从阿里云删除  阿里云会有大量冗余的文件
     */
    import axios from 'axios'
    import CooKies from 'vue-cookie'

    export default {
        name: 'DzAvatorUpload',
        props: {
            // 图片大小限制
            size: {
                type: Number | String,
                default: '100M'
            },
            filelimit: {
                type: Number | String,
                default: 1
            },
            idex: {
                type: Number | String,
                default: () => {
                    return -1;
                }
            },
            limit: {
              type: Boolean,
              default: false
            },
            // 初始图片
            defaultImg: {
                type: String,
                default:''
            },
            // 是否可以删除
            canDelete: {
              type: Boolean,
              default: true
            },
            width: {
              type: String,
              default: '220px'
            },
            height: {
              type: String,
              default: '132px'
            },
            tip: {
              type: String,
              default: '添加文件'
            },
            iconStyle:{
              type: Object,
              default: function() {
                  return {
                    width: '20%',
                    height: '25%'
                  }
              }
            },
            dragble: {
              type: Boolean,
              default: true
            },
            // 上传到哪个文件夹
            context: {
                default: 'information-gather'
            },
            disabled:{
                type: Boolean,
                default: false
            },
            type: {
              type: String,
              default: 'Img'
            },
            shape: {
              type: String,
              default: 'button'
            }
        },
        data () {
            return {
                imageUrl: '',
                uploadUrl: '',
                serverHost: '',
                tokenHost: '',
                deleteShow: false,
                viewYs: '?x-oss-process=video/snapshot,t_500,f_png,w_600,m_fast',
                uploadPercent: 0,
                showProgress: false,
                // 临时存放文件的名字
                tempFilesName: []
            }
        },
        created () {
            this.imageUrl = this.defaultImg
            this.tokenHost = process.env.VUE_APP_BASE_URL
        },
        mounted () {
            // if (this.fileUrls.length >= this.maxCount) {
            //     document
            //         .getElementsByClassName("dz-upload-photo")[0]
            //         .getElementsByClassName("el-upload ")[0].style.display = "none";
            // }
        },
        methods: {
            handleMouseEnter: function () {
              if (this.canDelete) {
                this.deleteShow = true
              }
            },
            handleMouseLeave: function () {
              if (this.canDelete) {
                this.deleteShow = false
              }
            },
            handlePreview: function (imageUrl) {
                window.open(imageUrl)
            },
            /**
             * 上传前的回调函数
             */
            beforeUpload: function (file) {
                // 上传大小限制
                if (file.size / 1024 > this.size) {
                    this.dz.showMessage('error','超过限制大小' + this.sizeLimit / 1024 + 'M')
                    return false
                }
                // 上传格式限制
                let types =  ['text/plain', 'application/pdf', 'application/rtf', 'application/vnd.ms-works', 'application/msword', 'application/vnd.ms-excel', 'application/vnd.ms-powerpoint', 'application/vnd.openxmlformats-officedocument.wordprocessingml.document', 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet', 'application/vnd.openxmlformats-officedocument.presentationml.presentation'];
                // let tip = ''
                // if (this.type === 'Img') {
                //   types = ['image/jpg', 'image/jpeg', 'image/png', 'image/gif']
                //   tip = '请上传图片'
                // } else {
                //   types = ['video/mp4']
                //   tip = '请上传视频文件'
                // }
                if (types.indexOf(file.type) === -1) {
                    this.dz.showMessage('error',"请上传规定的文件格式")
                    return false
                }
                return true
            },
            // 开始上传
            Upload (fileObj) {
              let file = fileObj.file
              // 文件后缀
              let filePostfix = ''
              let _this = this
              if (file.name.indexOf('.') > -1) {
                  let fileTxt = file.name.split('.')
                  filePostfix = fileTxt[fileTxt.length - 1]
              }
              let randomKey = this.dz.getRandomString()

              // 文件名临时储存
              this.tempFilesName.push({
                name: file.name,
                size: file.size,
                filePostfix: filePostfix,
                key: randomKey
              })
              this.disableFlg = true
              // 获取后端签名
              let getSign = new Promise((resolve) => {
                let res = JSON.parse(CooKies.get('aiclass_un_info_sign'))
                let timestamp = Date.parse(new Date()) / 1000

                if (res && res.expire -3 > timestamp) {
                  resolve(res)
                } else {
                  axios({
                    method: 'post',
                    url: '/oss/get-token',
                    baseURL: this.tokenHost,
                    data: {
                       context: this.context
                    }
                  }).then((res)=>{
                    if (res.data.code === 0) {
                      let data = res.data.content
                      CooKies.set('aiclass_un_info_sign',JSON.stringify(data), { expires: data.expire +'s' })
                      resolve(res.data.content)
                    }
                  }).catch(()=>{
                    _this.disableFlg = false
                    _this.deleteTempFiles(randomKey)
                    _this.dz.showMessage('error','上传失败')
                  })
                }
              })

              getSign.then((sign)=>{
                _this.serverHost = sign.host
                let ossData = new FormData()

                ossData.append('name', file.name)
                ossData.append('key', this.context + '/' +  randomKey + '.' +filePostfix)
                ossData.append('policy',sign.policy)
                ossData.append('OSSAccessKeyId',sign.accessid)
                ossData.append('success_action_status',200)
                ossData.append('signature',sign.signature)
                ossData.append('callback',sign.callback)
                // ossData.append('callback','')
                ossData.append('file', file)

                let xhr = new XMLHttpRequest()
                xhr.upload.addEventListener('progress', _this.handleProgress , false)

                xhr.onreadystatechange = () => {
                  if (xhr.readyState === 4 && xhr.status === 200) {
                    let data = JSON.parse(xhr.responseText)
                    _this.afterUpload(data , randomKey)
                    this.disableFlg = false
                  }

                  if (xhr.readyState === 4 && xhr.status !== 200) {
                    // 上传失败
                    this.disableFlg = false
                    _this.deleteTempFiles(randomKey)
                    _this.dz.showMessage('error','上传失败')
                  }
                }

                xhr.open('POST', sign.host)
                xhr.send(ossData)
              })
            },
            // 进度条
            handleProgress (e) {
              if (e.lengthComputable) {
                let percent = Math.floor(event.loaded / event.total * 100)
                if (percent > 100) {
                  percent = 100
                }
                this.$emit('progress-change', percent)
                this.uploadPercent = percent
                this.showProgress = percent === 100 ? false : true
              }
            },
            // 上传失败或有修改时
            deleteTempFiles (key) {
              for(let i = 0;i<this.tempFilesName;i++){
                if (key === this.tempFilesName[i].key) {
                  this.tempFilesName.splice(i,1)
                }
              }
            },
            getTempFile (key) {
              let file = {}
              this.tempFilesName.forEach((item)=>{
                if (item.key === key) {
                  file = item
                }
              })
              return file
            },
            /**
             * 上传成功的回调函数
             */
            afterUpload: function (response, key) {
                let fileItem = this.getTempFile(key)
                let newFile = {
                    name: fileItem.name,
                    url: this.serverHost + '/' + response.object,
                    size: fileItem.size,
                    type: fileItem.filePostfix
                }
                this.imageUrl = newFile.url
                if (this.idex > -1) {
                    let data={
                        result:newFile,
                        index:this.idex
                    }
                    this.$emit('upload-success', data)
                } else {
                    this.$emit('upload-success', newFile)
                }
            },
            /**
             * 删除的回调函数
             */
            removeUpload () {
                this.imageUrl = ''
                if (this.idex > -1) {
                    let data={
                        result:this.imageUrl,
                        index:this.idex
                    }
                    this.$emit('upload-success', data)
                } else {
                    this.$emit('upload-success', this.imageUrl)
                }
                this.deleteShow = false
            }
        },
        watch: {
          // 仅第一次获取时赋值
          defaultImg(val,oldVal) {
            if (!oldVal) {
                this.$nextTick(() => {
                    this.imageUrl = val
                })
            }
          }
        }
    }
</script>

<style lang="less">
    .dz-upload-photo{
        display: inline-block;
        border-radius: 6px;
        vertical-align: middle;
        .el-upload.el-upload--picture-card{
            width: 100%;
            height: 100%;
            position: relative;
            transition: all .6s;
            text-align: center;
            font-size: 14px;
            color: #fff;
            line-height: 40px;
            border: none;
            i{
              font-size: 16px;
              color: #fff;
            }
        }
        .el-upload--picture-card:hover, .el-upload:focus{
            border-color: #1EB2FE;
        }
        .el-upload-dragger{
          width: 100%;
          height: 100%;
          border-radius: 6px;
          >div{
            width: 100%;
            height: 100%;
          }
        }
        .el-upload-dragger:hover, .el-upload-dragger:focus{
          border-color: #1EB2FE;
          color:  #1EB2FE;
        }
        .el-upload.el-upload--picture-card .card{
          width: 100%;
          height: 100%;
          border: 1px dashed #ccc;
          color: #ccc;
          font-size: 12px;
          line-height: 12px;
          .tip{
            margin-top: 17px;
          }
          .el-icon-upload{
            margin-top: 17px;
            font-size: 24px;
            color: #1EB2FE;
          }
        }
        .el-button{
            width: 120px;
            height: 40px;
            border-radius: 20px;
            font-size: 16px;
            >span{
                display: inline-block;
            }
        }
    }
</style>
```