### Color & Size Unit

#### Color
##### 1. Names
CSS 内置的 [100 多种](https://developer.mozilla.org/en-US/docs/Web/CSS/color_value)颜色的名字，可以直接使用。

```css
body { color: black;}
a { color: orange;}
```

##### 2. rgb, rgba

```css
a { color: rgb(219, 78, 68);}
body { color: rgba(0, 0, 0, 0.8);}
```

##### 3. hsl, hsla

```css
/*the Hue a value ranging from 0 to 360, defines which color you want. 颜色*/
/*the Saturation percentage, ranging from 0% to 100%, defines how much of that color you want. 饱和度*/
/*the Lightness percentage, ranging from 0% to 100%, defines how bright you want that color to be. 亮度*/
a { color: hsl(4, 68%, 56%);}
/*a 代表 alpha*/
body { color: hsla(4, 68%, 56%, 0.5);}
```

##### 4. Hexadecimal
没什么好说的，就是用 16 进制表示颜色嘛~

```css
body { background-color: #e42331; }
```

#### Size
CSS 中有很多属性需要 size 的单位，比如：
- font-size
- border-width
- margin
- left/right/top/bottom

而 CSS 中的 size 单位有以下几种：
- px
- %
- em

##### 1. px

```css
/* px 代表的是绝对宽度，是不会改变的，当在设置 position 和 spacing 的时候广泛使用 */
body { width: 400px;}
```

##### 2. %

```css
/* % 是相对的单位，它的百分比是根据它的父级比的 */
strong { font-size: 150%;}
```

##### 3. em

```css
/* em 也是相对的单位，它的大小是根据它的父级的 font-size 来的 */
/* 一改变 body 的 font-size，h1, h2, aside 的值都会相应地改变 */
body { font-size: 16px; }
h1 { font-size: 2em; }        /* = 32px */
h2 { font-size: 1.5em; }      /* = 24px */
aside { font-size: 0.75em; }  /* = 12px */
```

##### 4. rem

```css
/* em 是根据父级，而 rem 是根据 html，可以理解为 root-em */
html { font-size: 18px;}
body { font-size: 1rem;}     /* = 18px */
h1 { font-size: 2rem;}       /* = 36px */
h2 { font-size: 1.5rem;}     /* = 27px */
```
