## Input placeholder属性样式修改(颜色,大小,位置)
```html
 1 <!DOCTYPE html>
 2 <html>
 3 <head>
 4     <meta charset="utf-8">
 5     <title></title>
 6     <style>
 7     
 8     input::-webkit-input-placeholder {
 9         /* placeholder颜色  */
10         color: #aab2bd;
11         /* placeholder字体大小  */
12         font-size: 12px;
13         /* placeholder位置  */
14         text-align: right;
15     }
16     input {
17         border: 1px solid red;
18     }
19     </style>
20 </head>
21 <body>
22     
23     <input type="text" placeholder="请输入手机号">
24     
25 </body>
26 </html>
```
*间距用padding*