---
layout: post
title: 暂存文件中的一部分
tags: [git]
published: false
---

刚刚修改文件，忘了暂存，之后又修改了文件的另一部分。
给我带来了一个问题：要想提交先前的修改，得先把之后的修改给抹掉。
不过，我在stackoverflow上发现了[类似的问题][]。

这是community wiki的解答。

> You can do `git add -p filename.x`, and it'll ask you what you want to stage. You can then:

> hit `s` to split whatever change into smaller hunks. This only works if there is at least one unchanged line in the "middle" of the hunk, which is where the hunk will be split.

> then hit either:

> - `y` to stage that hunk, or

> - `n` to not stage that hunk, or

> - `e` to manually edit the hunk (useful when git can't split it automatically)

> - and `d` to exit or go to the next file.

> - use `?` to get the whole list of available options.

> If the file is not in the repository yet, do first `git add -N filename.x`. Afterwards you can go on with `git add -p filename.x`.

主要是使用 `git add -p filename`命令。git manual 给出了详细解释：

```
       -p, --patch
           Interactively choose hunks of patch between the index and the work
           tree and add them to the index. This gives the user a chance to
           review the difference before adding modified contents to the index.

           This effectively runs add --interactive, but bypasses the initial
           command menu and directly jumps to the patch subcommand. See
           "Interactive mode" for details.
```

wiki最后还提到了 `git add -N filename.x`。`-N`的解释是：

```
       -N, --intent-to-add
           Record only the fact that the path will be added later. An entry
           for the path is placed in the index with no content. This is useful
           for, among other things, showing the unstaged content of such files
           with git diff and committing them with git commit -a.
```

### 结语

幸运的是，我的两次修改发生在不同地方；如果是同一个地方，那可能就要手动恢复了。

最后再给stackoverflow一个赞。


[类似的问题]: http://stackoverflow.com/questions/1085162/how-can-i-commit-only-part-of-a-file-in-git
