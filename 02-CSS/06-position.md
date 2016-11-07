### Position

position 有几个备选的值：
- static
static 是默认的值

- relative
很好理解，位置是相对的，比较的对象是它原本的位置。
- absolute
先介绍一下 positioned 的概念：当一个元素的 position 是 relative || absolute || fixed 的时候，那它就 positioned 了。
而当 position 是 absolute 的时候，这个元素的位置是根据它第一个已经 positioned 的祖先而确定的。

```html
<section>
  I'm in position relative.
  <p>
    I'm in position absolute!
  </p>
</section>
```

```css
section {
  background: gold;
  height: 200px;
  padding: 10px;
  position: relative; /* This turns the <section> into a point of reference for the <p> */
}

p {
  background: limegreen;
  color: white;
  padding: 10px;
  position: absolute; /* This makes the <p> freely movable */
  bottom: 10px; /* 10px from the bottom */
  left: 20px; /* 20px from the left */
}
```

- fixed
这个跟 absolute 很类似，只不过它真的是固定在一个位置了，边滚动屏幕的时候也不会跟着走，像极了网络上的牛皮癣小广告（摊手）
