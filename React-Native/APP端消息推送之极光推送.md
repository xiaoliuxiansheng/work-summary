## 选择其原因
相对接入成本低、成熟度高、文档完善、推送渠道支持多、有自建社区、新版本更新及时、有一定的免费额度。

### [github 地址](https://github.com/jpush/jpush-react-native)

## 自我使用总结

### 1. 安装
```javascript 1.8
npm install jpush-react-native --save
npm install jcore-react-native --save
```
### 2. 配置
#### 2.1 android
* 进入[极光推送官网](https://www.jiguang.cn/) 注册账号 
* 进入开发者平台 创建android应用 选择所需要的
产品服务 设置应用包名为对应的项目anroid包名 提交并组装sdk
* 进入android/app/build.gradle 新增
```javascript 1.8
android {
    ···
    defaultConfig {
         ...
         manifestPlaceholders = [
             JPUSH_APPKEY: "在此替换你的APPKey",         //APPKey（上一步创建应用对应的APPKey）
             JPUSH_CHANNEL: "yourChannel"        //在此替换你的channel 可自定义
         ]
    }
}
```
```javascript 1.8
dependencies {
    ···
    implementation project(':jpush-react-native')  // 添加 jpush 依赖
    implementation project(':jcore-react-native')  // 添加 jcore 依赖
    ···
}
```
* 进入android/settings.gradle 新增
```javascript 1.8
···
include ':jpush-react-native'
project(':jpush-react-native').projectDir = new File(rootProject.projectDir, '../node_modules/jpush-react-native/android')
include ':jcore-react-native'
project(':jcore-react-native').projectDir = new File(rootProject.projectDir, '../node_modules/jcore-react-native/android')
```

* 进入android/app/src/main/AndroidManifest.xml 新增
```javascript 1.8
    <application>
        ···
        <meta-data
                android:name="JPUSH_CHANNEL"
                android:value="${JPUSH_CHANNEL}" />
        <meta-data
                android:name="JPUSH_APPKEY"
                android:value="${JPUSH_APPKEY}" />
        ···
    </application>
```
### 2.2 基础react native 代码（在项目首页中执行）
```javascript 1.8
import JPush from 'jpush-react-native';
// 可进入node_modules/jpush-react-native/index.js  针对项目需要查看具体方法 
componentDidMount() {
        JPush.init();
        //连接状态
        this.connectListener = result => {
            console.log("connectListener:" + JSON.stringify(result))
        };
        JPush.addConnectEventListener(this.connectListener);
        //通知回调
        this.notificationListener = result => {
            console.log("notificationListener:" + JSON.stringify(result))
        };
        JPush.addNotificationListener(this.notificationListener);
        //本地通知回调
        this.localNotificationListener = result => {
            console.log("localNotificationListener:" + JSON.stringify(result))
        };
        JPush.addLocalNotificationListener(this.localNotificationListener);
        //自定义消息回调
        this.customMessageListener = result => {
            console.log("customMessageListener:" + JSON.stringify(result))
        };
        JPush.addCustomMessagegListener(this.customMessageListener);
        //tag alias事件回调
        this.tagAliasListener = result => {
            console.log("tagAliasListener:" + JSON.stringify(result))
        };
        JPush.addTagAliasListener(this.tagAliasListener);
        //手机号码事件回调
        this.mobileNumberListener = result => {
            console.log("mobileNumberListener:" + JSON.stringify(result))
        };
        JPush.addMobileNumberListener(this.mobileNumberListener);
    }
```
### 3.消息推送（必须真机调试 模拟器调试获取不到设备id 无法成功调试 真机调试时候 应设置该设备允许通知）
进入开发者平台 选择发送通知 编辑消息 设置目标人群即可
