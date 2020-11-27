
### canvas 签名板在h5的应用 插件：signature_pad  以下为基于vue vux的使用
* 组件
```javascript 1.8
<template>
  <div id="signature">
    <x-dialog title="" :show.sync="show" :close-on-click-modal="false" @close="close">
      <div class="wrapper">
        <div class="title">请在书写区手写姓名以确认本人已知悉此通知</div>
        <p  id="bkgroundp"></p>
        <!--<img src=""/>-->
        <canvas id="signature-canvas" class="signature-pad" width=356 height=300></canvas>
      </div>
      <div class="btn canvas-unselect">
        <x-button id="cancle" @click.native="close">取消</x-button>
        <x-button id="save" type="primary" @click.native="submit" :disabled="btnDisabled">确定</x-button>
        <!--<x-button id="reset" @click.native="reset">重置</x-button>-->
      </div>
    </x-dialog>
  </div>
</template>

<script type="text/javaScript">
    import SignaturePad from 'signature_pad'
    import { XDialog } from 'vux'
    export default {
        name: 'Signature',
        props: {
            dialogVisible: {
                type: Boolean,
                default: false
            },
            btnDisabled: {
                type: Boolean,
                default: false
            },
            // 上传上下文,必填，在OSS中相当于前缀文件夹
            context: {
                type: String,
                default: 'notices-h5',
                required: false
            },
        },
        components: {XDialog},
        data () {
            return {
                signaturePad: '',
                show: false
            }
        },
        created () {
            // canvas.setAttribute('width',170);
            // let canvas =document.getElementById("signature-canvas")
            // canvas.setAttribute('width',clientWidth+'px');
            // console.log(clientWidth)
        },
        mounted () {
            // 动态设置canvas的高度宽度 解决屏幕大的手机 点击地方不准确
            var  clientWidth = document.documentElement.clientWidth;
            let canvas = document.getElementById("signature-canvas")
            canvas.setAttribute('width',`${clientWidth*0.95}px`);
            canvas.setAttribute('height',`${clientWidth*0.95*0.842}px`);
        },
        filters: {},
        watch: {
            'dialogVisible' (val) {
                this.show = val
                this.$nextTick(() => {
                    this.initData()
                })
            }
        },
        methods: {
            close () {
                this.signaturePad.clear()
                this.$emit('close')
            },
            reset () {
                this.signaturePad.clear()
            },
            submit () {
                if (this.signaturePad.isEmpty()) {
                    this.$vux.toast.text('请签署姓名')
                    return false
                } else {
                    let data = this.signaturePad.toDataURL('image/png')
                    this.GetOssToken(data)
                }
            },
            dataURLtoFile (dataurl, filename) {
                let arr = dataurl.split(',');
                let mime = arr[0].match(/:(.*?);/)[1];
                let bstr = atob(arr[1]);
                let n = bstr.length;
                let u8arr = new Uint8Array(n);
                while (n--) {
                    u8arr[n] = bstr.charCodeAt(n);
                }
                return new File([u8arr], filename, {type: mime});
            },
            GetOssToken (file) {
                let _this = this
                return this.$SHNet.postJson(process.env.VUE_APP_BASE_URL + "/oss/get-token", { context: this.context }).then(res =>{
                        _this.uploadFile(res.content, file)
                    },()=>{
                        _this.$vux.toast.show({
                            text: '网络异常，请稍后再试'
                        })
                    })
            },
            initData () {
                var canvas = document.querySelector('canvas')
                var signaturePad = new SignaturePad(canvas, {
                    backgroundColor: 'rgba(255, 255, 255, 0)',
                    penColor: 'rgb(0, 0, 0)',
                    minWidth: 2,
                    maxWidth: 3
                })
                this.signaturePad = signaturePad
            },
            uploadFile (tokenObj, file) {
                this.$emit('upload-begin', file)
                let uploadUrl = tokenObj['host'] || ''
                let formData = new FormData()
                const data = new Date().getTime()
                console.log(formData.url);
                formData.append('name', `${data}.png`)
                formData.append('key', this.context + '/' + `${data}.png`)
                formData.append('policy', tokenObj['policy'] || '')
                formData.append('OSSAccessKeyId', tokenObj['accessid'] || '')
                formData.append('success_action_status', '200')
                formData.append('callback', tokenObj['callback'] || '')
                formData.append('signature', tokenObj['signature'] || '')
                formData.append('file', this.dataURLtoFile( file, `${data}.png`))
                let xhr = new XMLHttpRequest()
                xhr.open('POST', uploadUrl)
                xhr.send(formData)
                xhr.onload = () => {
                    if (xhr.status === 200) {
                        this.$emit('resulturl',uploadUrl + '/'+ JSON.parse(xhr.response).object)
                    }
                }
            }
        }
    }
</script>

<style lang="less">
  #signature{
    .weui-dialog{
      width: 100%;
      max-width: 95%;
    }
    .canvas-unselect {
      -webkit-touch-callout: none; /* iOS Safari */
      -webkit-user-select: none; /* Chrome/Safari/Opera */
      -khtml-user-select: none; /* Konqueror */
      -moz-user-select: none; /* Firefox */
      -ms-user-select: none; /* Internet Explorer/Edge */
      user-select: none; /* Non-prefixed version, currently not supported by any browser */
    }
    .wrapper {
      position: relative;
      width:100%;
      height:21.75rem;
      -moz-user-select: none;
      -webkit-user-select: none;
      -ms-user-select: none;
      user-select: none;
      .signature-canvas{
        width: 100%;
      }
    }
    .title{
      width: 15.88rem;
      margin: 0 auto;
      text-align: center;
      font-size:0.94rem;
      font-family:PingFang SC;
      font-weight:500;
      color:rgba(85,85,85,1);
      line-height:1.56rem;
    }
    p {
      background-color: #D8E2E9;
      position: absolute;
      left: 0;
      top: 3.2rem;
      width:100%;
      height:18.75rem;
      border-radius: 10px;
    }

    .signature-pad {
      position: absolute;
      left: 0;
      top: 3.2rem;
      width:100%;
      height:18.75rem;
    }
    .btn{
      box-sizing: border-box;
      padding: 0.4rem 0.3rem;
      /*display: flex;*/
      /*justify-content: space-around;*/
      .weui-btn{
        /*display: flex;*/
        width:7.66rem;
        height:2.81rem;
        font-size:1.13rem;
        line-height: 2.81rem;
        font-family:PingFang SC;
        font-weight:bold;
        color:rgba(30,178,254,1);
        display: inline-block;
        border-radius:0.2rem;
        /*vertical-align: middle;*/
      }
      :nth-child(2) {
        margin-left: 0.94rem;
        background:rgba(30,178,254,1);
        font-size:1.13rem;
        font-family:PingFang SC;
        font-weight:bold;
        color:rgba(255,255,255,1);
      }
    }
  }
</style>
```
* 使用方法
```javascript 1.8
<CanvasSignature :dialogVisible ="show" @resulturl="submitSign" @close="show = false"
                     :btnDisabled="btnDisabled"></CanvasSignature>
```
> 精髓
* 动态设置canvas宽度 高度 解决在屏幕大的手机 点击位置不准确
```javascript 1.8
var  clientWidth = document.documentElement.clientWidth;
let canvas = document.getElementById("signature-canvas")
canvas.setAttribute('width',`${clientWidth*0.95}px`);
// 0.95 =>设置canvas的宽度 为屏幕宽度的0.95  0.842 => 表示设置画布 宽高比例 根据不同的需求 自定义即可
canvas.setAttribute('height',`${clientWidth*0.95*0.842}px`);

```

* dataURLtoFile (dataurl, filename) 
> 因为通过signature_pad 画出来的图形为64进制文件流 需转成我们需要的格式 在这里 通过这个函数转为文件的格式（image/png）
```javascript 1.8
dataURLtoFile (dataurl, filename) {
                let arr = dataurl.split(',');
                let mime = arr[0].match(/:(.*?);/)[1];
                let bstr = atob(arr[1]);
                let n = bstr.length;
                let u8arr = new Uint8Array(n);
                while (n--) {
                    u8arr[n] = bstr.charCodeAt(n);
                }
                return new File([u8arr], filename, {type: mime});
            }
```
