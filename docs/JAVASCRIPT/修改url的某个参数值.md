## 修改url的某个参数值
```javascript 1.8
function replaceParamVal(paramName,replaceWith) {
    var oUrl = this.location.href.toString();
    var re=eval('/('+ paramName+'=)([^&]*)/gi');
    var nUrl = oUrl.replace(re,paramName+'='+replaceWith);
    this.location = nUrl;
　　window.location.href=nUrl
}
```
* 使用
replaceParamVal("userId","333")