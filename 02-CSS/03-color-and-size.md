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
body{ color: rgba(0, 0, 0, 0.8);}
```

##### 3. hsl, hsla

```css
/*the Hue a value ranging from 0 to 360, defines which color you want. 颜色*/
/*the Saturation percentage, ranging from 0% to 100%, defines how much of that color you want. 饱和度*/
/*the Lightness percentage, ranging from 0% to 100%, defines how bright you want that color to be. 亮度*/
a{ color: hsl(4, 68%, 56%);}
/*a 代表 alpha*/
body{ color: hsla(4, 68%, 56%, 0.5);}
```

##### 4. Hexadecimal
没什么好说的，就是用 16 进制表示颜色嘛~

```css
body { background-color: #e42331; }
```
