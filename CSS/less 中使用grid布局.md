# less中使用grid布局 CSS网格列被转换为不同的值
#### 使用grid布局通常会使用 ‘/’ 符号 但是 直接使用会导致 最终转义的css属性值 为数于数相除的结果
### 解决方案 使用less中的转义（Escaping）
```css
grid-area: ~"2/2/3/3"; // 属性前后分别用该写法代替即可
```
#### Escaping：
转义（Escaping）允许你使用任意字符串作为属性或变量值。任何 ~"anything" 或 ~'anything' 形式的内容都将按原样输出，除非 interpolation
