## 项目打包后 路径出现问题
### 解决方案如下
* 在 vue.config.js中配置 baseUrl:'./' ===> process.env.BASE_API：'./'
* 配置 VUE_APP_PUBLIC_PATH=./
