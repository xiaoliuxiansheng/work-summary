## 时间戳转日期
```javascript 1.8
formatDate: function (value) {
    // value 为毫秒格式
    let date = new Date(Number(value))
    let y = date.getFullYear()
    let MM = date.getMonth() + 1
    MM = MM < 10 ? ('0' + MM) : MM
    let d = date.getDate()
    d = d < 10 ? ('0' + d) : d
    let h = date.getHours()
    h = h < 10 ? ('0' + h) : h
    let m = date.getMinutes()
    m = m < 10 ? ('0' + m) : m
    return y + '-' + MM + '-' + d
}
```