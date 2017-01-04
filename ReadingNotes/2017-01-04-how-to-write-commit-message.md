# 如何写好 commit message？

Commit message 是每个开发人员每天都要写的东西，可是大多数人都会以一个 bug fix 或者 update xxx 草草了事。那么什么是好的 commit message 呢？我看到了一篇[好文](http://chris.beams.io/posts/git-commit/) ，顺手给大家总结了一下

比如，这是一条 commit message，首先我们要搞清楚 subject 和 body。

![](http://ww3.sinaimg.cn/large/5cc3eefejw1fbf1lbtx1dj20ze0l6qeq.jpg)

### 1. Subject 和 body 之间要留一个空行

- 这是 `git commit` 命令的[推荐做法](https://www.kernel.org/pub/software/scm/git/docs/git-commit.html#_discussion)
* 很多 git 的命令依赖于 subject 和 body 中的空行，比如 `git log --oneline`，`git shortlog`
* PS. 如果 commit 内容较少，没必要写 body

```
// good
Summarize changes in around 50 characters or less

More detailed explanatory text, if necessary. Wrap it to about 72
characters or so. In some contexts, the first line is treated as the
...

// bad
Summarize changes in around 50 characters or less
More detailed explanatory text, if necessary. Wrap it to about 72
characters or so. In some contexts, the first line is treated as the
...
```

### 2. 把 subject 控制在 50 个字符之内

如图，不解释。

![](http://ww4.sinaimg.cn/large/5cc3eefejw1fbf1lb459zj21k40icn0j.jpg)

### 3. Subject 的首字母大写

不觉得开头大写很优美吗？

```
// good
Accelerate to 88 miles per hour

// bad
accelerate to 88 miles per hour
```

### 4. Subject 的结尾不要加标点符号

Subject 尽量简明扼要，标点符号不是必须的。

```
// good
Open the pod bay doors

// bad
Open the pod bay doors.
```

### 5. 在 subject 中，使用 imperative 的语句

先来看看什么是 imperative 和 indicative

```
// imperative（命令性） VS indicative（陈述性）
Empty the bin, John. // imperative
John empties the bin. // indicative
```

Commit 中 的 subject 使用 imperative 的语句，比如：

```
// good
- Refactor subsystem X for readability
- Update getting started documentation
- Remove deprecated methods
- Release version 1.0.0

// bad
- Fixed bug with Y
- Changing behavior of X
- More fixes for broken stuff
- Sweet new API methods
```

有一个简单的做法可以看出你的 subject 是否符合了这条规定，就是把它套进 `If applied, this commit will {subject}`，如果语句通顺没有语病，说明你的 subject 是没有问题的。

```
// good
- If applied, this commit will refactor subsystem X for readability
- If applied, this commit will update getting started documentation
- If applied, this commit will remove deprecated methods
- If applied, this commit will release version 1.0.0
- If applied, this commit will merge pull request #123 from user/branch

// bad
- If applied, this commit will fixed bug with Y
- If applied, this commit will changing behavior of X
- If applied, this commit will more fixes for broken stuff
- If applied, this commit will sweet new API methods
```

在我看来，其实就是以动词原型开头（逃

### 6. 控制 body 每行的长度在 72 个字符

如果一行太长话，那真是没法看......

### 7. 用 body 说明这次 commit 的 what, why, how

能做到这一步的人真的很少。

PS. 我还是建议大家多用英文写 commit message，因为除了写英文代码注释以外，commit message 是唯一能在每日的工作中训练自己写好英语的机会了。好的 commit message 不光自己看着心里舒服，跟你协作开发的人看着也得劲。从这一刻开始，写好每一条 commit message 吧！

