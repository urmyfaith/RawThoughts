### CSS 的语法

CSS 的语法非常简单：

```css
selector { property: value; }

/* 其实也就是 */
who { what: how; }
```
#### 一、selector
知道了语法，那么如何找到一个元素成了首要的问题。CSS 通过以下几种方式选中一个元素：

##### 1. tag
直接通过选中标签和名字，便可以选中这个标签。

```css
a { /* Links */ }
p { /* Paragraphs */ }
ul { /* Unordered lists */ }
li { /* List items */ }
```

##### 2. class
我们可以给 HTML 的元素添加 class，然后通过选中 class 来达到选择某个元素的目的。

```html
<p class="date">
  Saturday Feb 21
</p>
<p>
  The event will be on <em class="date">Saturday</em>.
</p>
```

```css
/* 所有 class 为 date 的元素都会变为红色*/
.date {
  color: red;
}
```

##### 3. ID
跟 class 很像的还有 ID，不过语法略微不同：

```html
<h1 id="tagline">This heading will be orange.</h1>
```

```css
#tagline{ color: orange;}
```

注意，id 是 `#` 开头，而 class 是 `.` 开头。

##### 4. 其它
1. 可以通过 `tag+class` 的形式，把这些 selector 给组合起来：

```css
.date {
  color: red;
}

/* em 中带 date class 的都会变成蓝色 */
em.date {
  color: blue;
}
```

2. Hierarchy selectors

```css
/*选中 header 里的所有 a 标签*/
header a {
  color: red;
}
```

3. Pseudo-class selectors

```css
a {
  color: blue;
}

/*当 a 标签在 hover 状态的时候变成红色*/
a:hover {
  color: red;
}
```

#### 二、Inheritance
有一些属性是可以被继承的：

1. text color
2. font (family, size, style, weight)
3. line-height

#### 三、Priority

```
#id selectors are worth 100
.class selectors are worth 10
tag selectors are worth 1
```

所以当有多个 selectors 都指向一个元素的时候，只有优先级最高的会被应用到那个元素上面去：

```html
<p class="message" id="introduction">
  MarkSheet is a free HTML and CSS tutorial.
</p>
```

```css
/*这个时候只有 #introduction 会被应用，因为它有 100 分*/
#introduction { color: red;}
.message { color: green;}
p { color: blue;}
```

为了避免冲突：

1. 多使用 class，尽量避免 id
2. 不要对一个元素使用应用多个 class
3. 不要使用 inline css 的方式
