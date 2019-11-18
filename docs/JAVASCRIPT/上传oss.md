### 阿里云oss上传步骤
1. 获取oss token 示例如下
*  
```javascript 1.8
GetOssToken (file) {
                let _this = this
                return this.$axios.post(process.env.VUE_APP_BASE_URL + "/oss/get-token", { context: this.context }).then(res =>{
                        _this.uploadFile(res.content, file)
                    },()=>{
                        _this.$vux.toast.show({
                            text: '网络异常，请稍后再试'
                        })
                    })
            }
```     
2. 根据token信息上传文件至oss       
```javascript 1.8
 uploadFile (tokenObj, file) {
                this.$emit('upload-begin', file)
                let uploadUrl = tokenObj['host'] || ''
                let formData = new FormData()
                const data = new Date().getTime() //根据时间自定义文件名称
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
```            
