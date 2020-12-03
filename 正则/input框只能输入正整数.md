### input框只能输入正整数
```javascript 1.8
onKeypress="return(/[\d]/.test(String.fromCharCode(event.keyCode)))"
```
