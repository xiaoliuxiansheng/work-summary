## formData的主要用途有两个
1. 将form表单元素的name与value进行组合，实现表单数据的序列化，从而减少表单元素的拼接，提高工作效率。
2. 异步上传文件
### 具体使用方法
#### 一、创建formData对象
1. 创建一个空对象
```javascript 1.8
let formdata = new FormData();//利用FormData构造函数 创建一个空对象
formdata.append('name','value') //通过append() 方法追加数据 两个参数分别对应 name value
formdata.set('name','zhangsan')// 通过set()方法 设置 name 为‘name’ 的value值
formdata.get('name') //通过get()方法获取name 为‘zhangsan’ 的value值
```
2. 通过表单创建初始化FormData
```html
<form id="advForm">
    <p>广告名称：<input type="text" name="advName"  value="xixi"></p>
    <p>广告类别：<select name="advType">
        <option value="1">轮播图</option>
        <option value="2">轮播图底部广告</option>
        <option value="3">热门回收广告</option>
        <option value="4">优品精选广告</option>
    </select></p>
    <p><input type="button" id="btn" value="添加"></p>
</form>
```
通过表单元素作为参数，实现对formData的初始化：
```html
//获得表单按钮元素
var btn=document.querySelector("#btn");
//为按钮添加点击事件
btn.onclick=function(){
    //根据ID获得页面当中的form表单元素
    var form=document.querySelector("#advForm");
    //将获得的表单元素作为参数，对formData进行初始化
    var formdata=new FormData(form);
    //通过get方法获得name为advName元素的value值
    console.log(formdata.get("advName"));//xixi
    //通过get方法获得name为advType元素的value值
    console.log(formdata.get("advType"));//1 
}
```
3. 通过has(key)来判断是否存在对应的key值
```javascript 1.8
console.log(formdata.has('zhangsan'))// true
```
4. 通过delete(key)删除数据
```javascript 1.8
formdata.delete('zhangsan')
```
#### 二、通过XMLHttpRequest()发送数据
```javascript 1.8
 // var formdata=new FormData(document.getElementById("advForm"));
 let formdata = new FormData();
formdata.append('name','value')
formdata.get('name') 
    var xhr=new XMLHttpRequest();
    xhr.open("post","http://127.0.0.1/xxx");
    xhr.send(formdata);
    xhr.onload=function(){
        if(xhr.status==200){
            //...
        }
    }
```    
