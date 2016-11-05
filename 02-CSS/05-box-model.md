### Box Model

#### display
`display` 属性可以修改 HTML 元素的种类，比如 `<p>` 标签是 `block` 类型的，但是可以通过 `display` 修改为 `inline` 类型的（就像 `<span>` 一样）。

```css
p { display: inline; }
```

我们为什么要修改它们的种类呢？因为我们选择标签的时候是根据它们本身的种类，而不是它们显示的样子。比如我们要添加一个无序列表，但它的显示要是 `inline` 的，那显然 `<ul>` 是放在第一位的。

```html
<!-- 它们显示默认是 block 的 -->
<ul class="menu">
  <li>
    <a>Home</a>
  </li>
  <li>
    <a>Features</a>
  </li>
  <li>
    <a>Pricing</a>
  </li>
  <li>
    <a>About</a>
  </li>
</ul>
```

```css
/* 把它们都显示在一行 */
.menu li {
  display: inline;
}
```

- `display: none` 和 `visibility: hidden` 的区别
前者是完全把这个元素移除了，跟没有存在过一样，然而后者只是把它隐藏了，然而占用的位置还在。

#### width & height
注意一点，如果内容超出了 `width & height` 的区域，设置 `overflow` 属性可以避免超出的内容显示的有问题。

#### border & padding & marigin
