### text

#### font
通过 font-family 属性可以设置字体，这么多值是因为当前一种字体计算机没有装的时候，浏览器会加载后一种字体，当这些字体都没有的时候，会加载默认的字体。

```css
body{ font-family: Arial, Verdana, sans-serif;}
```

#### font property

```css
/* 比较常用的是以下三种，文档都可以查到就不再赘述了 */
p { font-size: 16px;}
h2 { font-style: italic;}
h2 { font-weight: bold;}
```

#### line-height
`line-height` 有三种单位，`px`, `em`, `%`，当是数字的时候，单位默认是数字

```css
/* 这个时候 line-height 是 16*1.5=24px */
body { font-size: 16px; line-height: 1.5;}
/* line-height 也可以被继承，这个时候 blockquote 的 line-height 是 18*1.5=27px */
blockquote{ font-size: 18px;}
```

#### text property
有一些 text 相关的属性，比如 `text-align, text-indent, text-shadow` 之类的，文档都可以查到，就不再说明。
