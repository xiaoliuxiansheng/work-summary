### 1、router: react-native-router-flux
>基于react-navigation/native 二次封装
### 2、字体图标：react-native-vector-icons
      遇到的问题：
      React Native CLI uses autolinking for native dependencies, but the following modules are linked manually: - react-native-vector-icons (to unlink run: "react-native unlink react-native-vector-icons")
解决方法：
>1、xcode 配置info.list 将fonts 复制到ios 文件夹中与项目名相同的文件夹中

>2、在info.list中将以下代码复制进去：
```javascript 1.8
<key>UIAppFonts</key>
<array>
  <string>AntDesign.ttf</string>
  <string>Entypo.ttf</string>
  <string>EvilIcons.ttf</string>
  <string>Feather.ttf</string>
  <string>FontAwesome.ttf</string>
  <string>FontAwesome5_Brands.ttf</string>
  <string>FontAwesome5_Regular.ttf</string>
  <string>FontAwesome5_Solid.ttf</string>
  <string>Foundation.ttf</string>
  <string>Ionicons.ttf</string>
  <string>MaterialIcons.ttf</string>
  <string>MaterialCommunityIcons.ttf</string>
  <string>SimpleLineIcons.ttf</string>
  <string>Octicons.ttf</string>
  <string>Zocial.ttf</string>
  <string>Fontisto.ttf</string>
</array>
```
### 3、teaset ui库 
>(使用起来有很多吭 建议不使用 比如里面的drawer 需要注意 手动关闭Drawer 时 会出发当前页面更新 导致state中的data复原，Drawer 里面的state数据更新需要处理）
### 4、时间日期选择器 
>import DatePicker from 'react-native-datepicker'
### 5、配置mobx  
>1、安装mobx
npm install mobx --save
>
>2、安装bable转码
npm install @babel/plugin-proposal-decorators
>
>3、创建 .babelrc 配置文件
```javascript 1.8
{
"presets": ["module:metro-react-native-babel-preset"],
"plugins": [
    ["@babel/plugin-proposal-decorators", { "legacy": true }],
    ["@babel/transform-runtime", {
    "helpers": true,
    "regenerator": false
    }]
]
}
```
>4、安装 以下 4个依赖包
yarn add @babel/core @babel/plugin-proposal-decorators @babel/plugin-transform-runtime @babel/runtime

>5、仓库绑定到根组件上
```javascript 1.8
import { Provider } from 'mobx-react'
 import store from './store'
<Provider store={store}>
  <Root></Root>
  </Provider>
```
### 6、uI库 antdesign 
配置antdesign:
>1.这是安装antd-mobile-rn
    yarn add @ant-design/react-native

>2.antd-mobile-rn里面有很多Icon和font如果需要引入，则下载
    yarn add @ant-design/icons-react-native

>3.在根目录创建.babelrc
    {
      "plugins": [
        ["import", { "libraryName": "@ant-design/react-native" }]
      ]
    }

>4.安装其他依赖
    yarn add @react-native-community/cameraroll @react-native-community/picker @react-native-community/segmented-control @react-native-community/slider @react-native-community/viewpager

>5.报错提示安装
    npm install babel-plugin-import --save
　　 npm install
### 7、地图 link 跳转第三方 
>第三方依赖：https://github.com/starlight36/react-native-map-linking
### 8、数据缓存方案：
>a、 AsyncStorage
一个简单的、异步的、持久化的 Key-Value 存储系统，它对于 App 来说是全局性的。有一个极大的缺点就是：只可以存储字符串。也就是说，如果需要将对象或者数组等存入到AsyncStorage中需要先将他们转为字符串
 demo:https://blog.csdn.net/z93701081/article/details/90905178（已过时）
最新官网推荐：import AsyncStorage from '@react-native-community/async-storage';
git地址：https://react-native-community.github.io/async-storage/docs/install
>
>b、react-native-sqlite || react-native-sqlite-storage
存放数据结构复杂的数据的时候，我们就会想起sqlite，sqlite这种跨平台的数据存储方式在ReactNative里也有对应的方式，那就是react-native-sqlite
### 9、React Native封装请求函数
```javascript 1.8
/**
 * @name: index
 * @author: LIULIU
 * @date: 2020-07-20 13:38
 * @description：index
 * @update: 2020-07-20 13:38
 */
/*
* 网络请求相关
* */
import {Toast} from '@ant-design/react-native';

export const BaseUrl = `http://example.net/`;

export default class Request {

    static get(url) {
        return new Promise((resolve, reject) => {
            fetch(BaseUrl + url)
                .then(response => {
                    if (response.ok) {
                        return response.text();
                    } else {
                        throw new Error('网络请求失败！');
                    }
                })
                .then(responseText => {
                    const responseJSObj = JSON.parse(responseText);
                    resolve(responseJSObj);
                })
                .catch(error => {
                    Toast.fail('访问异常')
                    reject(error);
                });
        });
    }

    /**
     * RN提供的fetch方法，是异步的，它本身就会返回一个Promise对象。但因为这里我们对它进行了封装，所以外面又包了一层Promise，来给fetch这个异步任务提供回调，这样外界才能拿到fetch的结果。
     *
     * @param url
     * @param params
     * @returns {Promise<any> | Promise}
     */
    static async post(url, params) {
        return new Promise(async (resolve, reject) => {
            fetch(BaseUrl + url, {
                method: 'POST',
                headers: {
                    Accept: 'application/json',
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(params),
            })
                .then(response => {
                    if (response.ok) {
                        // 请求到的response其实是一个Response对象，它是一个很原始的数据格式，我们不能直接使用，先获取它的JSON字符串文本格式
                        return response.text();
                    } else {
                        throw new Error('网络请求失败！');
                    }
                })
                .then(responseText => {
                    // 然后把JSON字符串序列化为JS对象
                    const responseJSObj = JSON.parse(responseText);
                    // 把请求成功的数据传递出去
                    resolve(responseJSObj);
                })
                .catch(error => {
                    // 把请求失败的信息传递出去
                    Toast.fail('访问异常')
                    reject(error);
                });
        });
    }
}
```
