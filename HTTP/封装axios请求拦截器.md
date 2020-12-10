### 封装axios请求拦截器
````javascript 1.8
import axios from 'axios';
import { message } from 'antd';
import config from './config.js' // 基于开发环境的配置文件
import {
  remove,
  findLast
} from 'lodash';
import storage from '../utils/storage.ts' // 验证信息配置文件

const baseWaitTime = 500; // 默认的等待时间500毫秒
const requestURLRate = []; // 如：{ api: '/api/standardRoles', timestamp: 1596597701181 }

/**
 * 请求出入口
 * @param {*} api 地址
 * @param {*} method 方法，默认为GET
 * @param {*} params 参数，默认为空对象
 * @param {*} maxRequestCycleCount 最大请求频次（与baseWaitTime结合），默认为1
 * @param {*} serverHost 接口主机地址
 * @param {*} headers 传入头部信息，默认为空对象
 */
export default function axiosRequest(api, method = 'GET', params = {}, headers = {}, maxRequestCycleCount = 1, serverHost = config.HOST) {

  // 针对非GET请求进行限流拦截
  if (method != 'GET') {

    let nowTimestamp = new Date().getTime(); // 当前时间戳

    // 去除当前接口指定周期外的数据
    remove(requestURLRate, (o) => {
      return o.api === api && o.timestamp < nowTimestamp - (maxRequestCycleCount * baseWaitTime);
    });

    // 获取上一次请求信息（一般同周期只有一个，防止处理意外）
    let hasRequestURLRate = findLast(requestURLRate, o => (o.api === api));

    if (hasRequestURLRate && maxRequestCycleCount !== 0) {

      message.warning('当前访问的频次过高，请适当放慢手速！', 1);

      // 为了保持数据完整性，返回数据与接口定义一致
      return {
        errcode: -100,
        msg: null
      };

    } else {
      requestURLRate.push({
        api,
        timestamp: new Date().getTime()
      });
    }
  }
  
  return new Promise((resolve, reject) => {
    const token = storage().get('TOKEN');
    axios({
      method,
      headers: {
        authorization: 'Bearer ' + token,
        ...headers,
      },
      url: (serverHost || global.G_SERVER_HOST) + api,
      data: params
    })
    .then((res) => {
      if (res.status === 200) {
        if( res.data.error === 1){
          // console.log()
          message.error(res.data.detail ||res.data.msg || '请求错误！');
           // todo：错误收集
          reject(res.data);
        }else if(res.data.error === 0 || !res.data.error){
          resolve(res.data);
        }
      }
    })
    .catch((error) => {
      console.log(error)
      if (error) {
        // todo：错误收集
        message.error('服务端发生逻辑错误！');
      }
      reject(error);

    })
  })
}
````
