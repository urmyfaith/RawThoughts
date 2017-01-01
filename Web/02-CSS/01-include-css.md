### CSS 的引入方式
#### 1. 作为属性

```html
<p style="color: red;">This text is important.</p>
```

#### 2. 写在 `<head>` 标签里

```html
<html>
  <head>
    <title>Hello World</title>
    <style type="text/css">
      p { color: red; }
    </style>
  </head>
  <body>
    <p>This paragraph will be red.</p>
  </body>
</html>
```

#### 3. 作为一个单独的文件

```html
<html>
  <head>
    <title>Hello World</title>
    <link rel="stylesheet" type="text/css" href="style.css">
  </head>
  <body>
    <p>This paragraph will be red.</p>
  </body>
</html>
```

```css
p { color: red; }
```
