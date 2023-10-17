# 智商50也能轻松看懂的git笔记BY碳宝

## 0.前言

本文仅是对常用的git功能进行记录，以基础为主，不会过多涉及原理和非常用功能。

本文仅是对git Book中的一部分内容进行整理和搬运，再加上一些本人整理的资料和个人的想法，几乎不生产知识而是知识的搬运工，所以可以随便转载。

git Book: [Git - Book (git-scm.com)](https://git-scm.com/book/en/v2)

本文列出的基本都是命令行的方式，git的图形界面也非常好用，但作用毕竟是命令行的子集，命令行用利索了gui版当然不在话下。

(可以使用GitHub for Desktop：[GitHub Desktop | Simple collaboration from your desktop](https://desktop.github.com/))

会持续更新，看心情吧

### 为什么要用git

通过进行项目管理，git可以很方便地让一群人一起工作。

### 什么时候用git

所有文本编辑的文件都适合用git，除此以外都不适合用git（取而代之的，你应该用网盘）。



## 1.获取git仓库

### (1)将尚未进行版本控制的本地目录转换为 Git 仓库(git init)

`$ git init`

该命令将创建一个名为 `.git` 的子目录，这个子目录含有你初始化的 Git 仓库中所有的必须文件，但是你的文件还没有被跟踪。

### (2)从其它服务器克隆一个已存在的 Git 仓库(git clone)

`$ git clone <url>`

比如，如果输入以下命令：

` $ git clone https://github.com/libgit2/libgit2`

这会在当前目录下创建一个名为 “libgit2” 的目录，并在这个目录下初始化一个 `.git` 文件夹， 从远程仓库拉取下所有数据放入 `.git` 文件夹，然后从中读取最新版本的文件的拷贝。



如果你想在克隆远程仓库的时候，自定义本地仓库的名字，你可以通过额外的参数指定新的目录名：

` $ git clone https://github.com/libgit2/libgit2 mylibgit`

这会执行与上一条命令相同的操作，但目标目录名变为了 `mylibgit`。



## 2.记录每次更新到本地仓库

工作目录下的每一个文件都不外乎这两种状态：**已跟踪** 或 **未跟踪**。 

已跟踪的文件是指那些被纳入了版本控制的文件（已经被git add过了的文件），在上一次快照中有它们的记录，在工作一段时间后， 它们的状态可能是未修改，已修改或已放入暂存区。

简而言之，已跟踪的文件就是 Git 已经知道的文件。

![Git 下文件生命周期图。](http://salieri-typora.oss-cn-shanghai.aliyuncs.com/img/markdown/lifecycle.png)

### (1)检查当前文件状态(git status)

`$ git status`

如果在获取git仓库后立即使用此命令，会有这样的输出：

```console
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
nothing to commit, working directory clean
```

这说明你现在的工作目录相当干净。

1. 所有已跟踪文件在上次提交后都未被更改过。 
2. 当前目录下没有出现任何处于未跟踪状态的新文件，否则 Git 会在这里列出来。

现在，在项目下创建一个新的 `README` 文件。 如果之前并不存在这个文件，使用 `git status` 命令，你将看到一个新的未跟踪文件：

```console
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Untracked files:
  (use "git add <file>..." to include in what will be committed)

    README

nothing added to commit but untracked files present (use "git add" to track)
```

未跟踪的文件意味着 Git 在之前的快照（提交）中没有这些文件；Git 不会自动将之纳入跟踪范围，除非你告诉它“我需要跟踪该文件”。

### (2)跟踪新文件(git add)

使用命令 `git add` 开始跟踪一个文件。 所以，要跟踪 `README` 文件，运行：
`$ git add README`
此时再运行 `git status` 命令，会看到 `README` 文件已被跟踪，并处于暂存状态：

```console
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)

    new file:   README
```

只要在 `Changes to be committed` 这行下面的，就说明是已暂存状态。 如果此时提交，那么该文件在你运行 `git add` 时的版本将被留存在后续的历史记录中。

### (3)暂存已修改的文件(git add)

假设有一个叫`CONTRIBUTING.md` 的已被跟踪的文件，我们把它修改了，然后运行`git status` 命令，会看到下面内容：

```console
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file:   README

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   CONTRIBUTING.md
```

文件 `CONTRIBUTING.md` 出现在 `Changes not staged for commit` 这行下面，说明已跟踪文件的内容发生了变化，但还没有放到暂存区。

要暂存这次更新，需要运行 `git add` 命令。

git add命令的具体作用：

1. 开始跟踪新文件
2. 把已跟踪的文件放到暂存区
3. 合并时把有冲突的文件标记为已解决状态

但是在本质上，add命令都是把某个文件（不论是新文件还是已跟踪的文件）放入暂存区，只是在实际作用上可以分为这三类。所以将这个命令理解为“精确地将内容添加到下一次提交中”而不是“将一个文件添加到项目中”要更加合适。

现在运行 `git add` 将“CONTRIBUTING.md”放到暂存区，然后再看看 `git status` 的输出：

```console
$ git add CONTRIBUTING.md
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file:   README
    modified:   CONTRIBUTING.md
```

如果此时又改了改`CONTRIBUTING.md`，再运行 `git status`，会有以下输出：

```console
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file:   README
    modified:   CONTRIBUTING.md

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   CONTRIBUTING.md
```

实际上 Git 只不过**暂存了你运行 `git add` 命令时的版本**。 

如果你现在提交，`CONTRIBUTING.md` 的版本是**你最后一次运行 `git add` 命令时的那个版本**，而不是你运行 `git commit` 时，**在工作目录中的当前版本**。 

所以，运行了 `git add` 之后又作了修订的文件，需要重新运行 `git add` 把最新版本重新暂存起来：

```console
$ git add CONTRIBUTING.md
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file:   README
    modified:   CONTRIBUTING.md
```

### (4)状态简览(git status -s)

`$ git status -s`或者`$ git status -short`

你将得到一种格式更为紧凑的输出。

```console
$ git status -s
 M README
MM Rakefile
A  lib/git.rb
M  lib/simplegit.rb
?? LICENSE.txt
```

**第一列**字符表示**版本库**与**暂存区**之间的比较状态。
**第二列**字符表示**暂存区**与**工作区**之间的比较状态。

`' '` （空格）表示文件未发生更改
`M` 表示文件发生改动。
`A` 表示新增文件。
`D` 表示删除文件。
`R` 表示重命名。
`C` 表示复制。
`U` 表示更新但未合并。
`?` 表示未跟踪文件。
`!` 表示忽略文件。

### (5)忽略文件(.gitignore)

有时候我们不希望某种格式的文件被版本控制。

我们可以创建一个名为 `.gitignore` 的文件，列出要忽略的文件的模式。 来看一个实际的 `.gitignore` 例子：

```console
*.[oa]
*~
```

第一行告诉 Git 忽略所有以 `.o` 或 `.a` 结尾的文件。

第二行告诉 Git 忽略所有名字以波浪符（~）结尾的文件。



在最简单的情况下，一个仓库可能只根目录下有一个 `.gitignore` 文件，它递归地应用到整个仓库中。

 然而，子目录下也可以有额外的 `.gitignore` 文件。子目录中的 `.gitignore` 文件中的规则只作用于它所在的目录中。

#### 1. gitignore规则优先级

1. 从命令行中读取可用的忽略规则
2. 当前目录定义的规则
3. 父级目录定义的规则，依次递推
4. $GIT_DIR/info/exclude 文件中定义的规则
5. core.excludesfile中定义的全局规则

#### 2. gitignore规则匹配语法

- 所有空行或者以 `#` 开头的行都会被 Git 忽略。

- 可以使用标准的 glob 模式匹配，它会递归地应用在整个工作区中。

  - 所谓的 glob 模式是指shell 所使用的简化了的正则表达式

    > 星号（`*`）匹配零个或多个任意字符；
    >
    > `[abc]` 匹配任何一个列在方括号中的字符 （这个例子要么匹配一个 a，要么匹配一个 b，要么匹配一个 c）；
    >
    >  问号（`?`）只匹配一个任意字符；
    >
    > 如果在方括号中使用短划线`[ - ]`分隔两个字符， 表示所有在这两个字符范围内的都可以匹配（比如 `[0-9]` 表示匹配所有 0 到 9 的数字）。 
    >
    > 使用两个斜杠（`//`）表示匹配任意中间目录，比如 `a//z` 可以匹配 `a/z` 、 `a/b/z` 或 `a/b/c/z` 等。
- 匹配模式可以以（`/`）开头防止递归。
- 匹配模式可以以（`/`）结尾指定目录。
- 要忽略指定模式以外的文件或目录，可以在模式前加上叹号（`!`）取反。

再看一个 `.gitignore` 文件的例子：

```
# 忽略所有的 .a 文件
*.a

# 但跟踪所有的 lib.a，即便你在前面忽略了 .a 文件
!lib.a

# 只忽略当前目录下的 TODO 文件，而不忽略 subdir/TODO
/TODO

# 忽略任何目录下名为 build 的文件夹
build/

# 忽略 doc/notes.txt，但不忽略 doc/server/arch.txt
doc/*.txt

# 忽略 doc/ 目录及其所有子目录下的 .pdf 文件
doc/**/*.pdf
```

#### 3. 一份十分详细的.gitignore文件列表

[github/gitignore: A collection of useful .gitignore templates](https://github.com/github/gitignore)



### (6)查看修改(git diff)

`$ git diff`

`git diff` 能通过文件补丁的格式更加具体地显示哪些行发生了改变，此命令比较的是**工作目录中当前文件**和**暂存区域快照**之间的差异。 也就是修改之后还没有暂存起来的变化内容。

假如再次修改 README 文件后暂存，然后编辑 `CONTRIBUTING.md` 文件后先不暂存

要查看尚未暂存的文件更新了哪些部分，不加参数直接输入 `git diff`：

```console
$ git diff
diff --git a/CONTRIBUTING.md b/CONTRIBUTING.md
index 8ebb991..643e24f 100644
--- a/CONTRIBUTING.md
+++ b/CONTRIBUTING.md
@@ -65,7 +65,8 @@ branch directly, things can get messy.
 Please include a nice description of your changes when you submit your PR;
 if we have to read the whole diff to figure out why you're contributing
 in the first place, you're less likely to get feedback and have your change
-merged in.
+merged in. Also, split your changes into comprehensive chunks if your patch is
+longer than a dozen lines.
```

#### 1. git rm --cached(--staged)

`$ git diff --staged	`或`$ git diff --cached`

这条命令将比对**已暂存文件**与**最后一次提交**(commit)的文件差异.

#### 2. git diff的插件版本

可以使用图形化的工具或外部 diff 工具来比较差异。 

可以使用 `$ git difftool` 命令来调用 emerge 或 vimdiff 等软件（包括商业软件）输出 diff 的分析结果。



### (7).提交更新(git commit)

提交的时候不会记录这些尚未暂存的变化，所以提前看清楚是不是想提交的都`git add`过了

`$ git commit`

默认是 Vim 的屏显方式

```console
# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
# On branch master
# Your branch is up-to-date with 'origin/master'.
#
# Changes to be committed:
#	new file:   README
#	modified:   CONTRIBUTING.md
#
~
~
~
".git/COMMIT_EDITMSG" 9L, 283C
```

在insert模式下写好注释后，输入:wq，就可以退出了，并且Git 会丢弃注释行，用你输入的提交说明生成一次提交。

#### 1. git commit -m

`$ git commit -m`

也可以在 `commit` 命令后添加 `-m` 选项，将提交信息与命令放在同一行，如下所示：

```console
$ git commit -m "Story 182: Fix benchmarks for speed"
[master 463dc4f] Story 182: Fix benchmarks for speed
 2 files changed, 2 insertions(+)
 create mode 100644 README
```

提交后它会告诉你，当前是在哪个分支（`master`）提交的，本次提交的完整 SHA-1 校验和是什么（`463dc4f`），以及在本次提交中，有多少文件修订过，多少行添加和删改过。

> 什么是校验和？
>
> 可以看我发布在微信公众号上的文章：https://mp.weixin.qq.com/s/X4fBbzhJuglcIwQVkqeDaA

#### 2. git commit -a

` $ git commit -a`

Git 会自动把所有已经跟踪过的文件暂存起来一并提交，从而跳过 `git add` 步骤：

```console
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   CONTRIBUTING.md

no changes added to commit (use "git add" and/or "git commit -a")
$ git commit -a -m 'added new benchmarks'
[master 83e38c7] added new benchmarks
 1 file changed, 5 insertions(+), 0 deletions(-)
```



### (8)移除文件(git rm)

要从 Git 中移除某个文件，就必须要从已跟踪文件清单中移除（确切地说，是从暂存区域移除），然后提交。

#### 1. rm(仅是从工作目录中手工删除文件)

`$ rm`

如果只是简单地从工作目录中手工删除文件，运行 `git status` 时就会在 “Changes not staged for commit” 部分（也就是 *未暂存清单*）看到。

```console
$ rm PROJECTS.md
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        deleted:    PROJECTS.md

no changes added to commit (use "git add" and/or "git commit -a")
```

所以说，rm只是删除了工作区的文件，没有删除暂存区和版本库中的文件。如果需要继续删除暂存区和版本库中的文件，还需要：

```console
$ git add PROJECTS.md
$ git commit -m 'delete PROJECTS.md'
```



#### 2. git rm

`$ git rm`

删除工作区文件，并且将这次删除放入暂存区。

注意：**要删除的文件是没有修改过的，就是说和当前==版本库文件==的内容相同。**否则`git rm`会报错

```console
$ git rm PROJECTS.md
rm 'PROJECTS.md'
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    deleted:    PROJECTS.md
```

之后通过`git commit`提交一下，就可以连版本库中的文件也会删掉了。



#### 3. git rm -f

`$ git rm  -f`

删除工作区和暂存区文件，并且将这次删除放入暂存区。

注意：**要删除的文件已经修改过，就是说和当前==版本库文件==的内容不同。**

(1) test文件修改过还没 git add 到暂存区

```console
$ git rm test.txt
error: the following file has local modifications:
    test.txt
(use --cached to keep the file, or -f to force removal)
```

(2) test文件修改过已经 git add 到暂存区

```cnosole
$ git add test.txt
$ git rm test.txt
error: the following file has changes staged in the index:
    test.txt
(use --cached to keep the file, or -f to force removal)
```

可见文件修改后不管有没有 git add 到暂存区，使用 git rm 命令删除都会报错。

执行`$ git rm  -f`：

```console
$ git rm -f test.txt
rm 'test.txt'
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        deleted:    test.txt
```

删除了工作区、暂存区的文件，之后通过`git commit`提交一下，就可以连版本库中的文件也会删掉了。

之所以非得加个-f(which refers to "force")，是因为修改后的文件还没有被commit，删除后git是找不回来修改的内容的，所以必须强制才能被删除，这是一种保护手段。



#### 4. git rm --cached

`$ git rm --cached`（在这里不存在--staged）

删除暂存区文件，但保留工作区的文件，并且将这次删除放入暂存区。

```console
$ git rm --cached test.txt
rm 'test.txt'
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        deleted:    test.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        test.txt

```

（注意这里文件取消了跟踪）之后通过`git commit`提交一下，就可以连版本库中的文件也会删掉了。



	#### 5. 速记

`rm` 	=	工作区

`git rm`	=	工作区	+	暂存区（文件与当前版本库文件内容**相同**）

`git rm -f`	=	工作区	+	暂存区（文件与当前版本库文件内容**不同**）

`$ git rm --cached`	=	暂存区



#### 6. 移动文件(git rm [file_name] [file_name])

Git 并不显式跟踪文件移动操作。 如果在 Git 中重命名了某个文件，仓库中存储的元数据并不会体现出这是一次改名操作。 不过 Git 非常聪明，它会推断出究竟发生了什么。

要在 Git 中对文件改名，可以这么做：

```console
$ git mv file_from file_to
```

它会恰如预期般正常工作：

```console
$ git mv README.md README
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    renamed:    README.md -> README
```

运行 `git mv` 就相当于运行了下面三条命令：

```console
$ mv README.md README
$ git rm README.md
$ git add README
```

如果你分开操作，Git也会立马意识到这是一次重命名。



### (9)查看提交历史(git log)

`$ git log`

不传入任何参数的默认情况下，`git log` 会按时间先后顺序列出所有的提交，最近的更新排在最上面。

这个命令会列出每个提交的 SHA-1 校验和、作者的名字和电子邮件地址、提交时间以及提交说明。

```console
$ git log
commit ca82a6dff817ec66f44342007202690a93763949
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Mon Mar 17 21:52:11 2008 -0700

    changed the version number

commit 085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Sat Mar 15 16:40:33 2008 -0700

    removed unnecessary test

commit a11bef06a3f659402fe7563abf99ad00de2209e6
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Sat Mar 15 10:31:28 2008 -0700

    first commit
```

#### 1. 常用参数

1. `-p`：按补丁格式显示每个更新的差异，比下一条`--stat`命令信息更全。

2. `--stat`：显示每次提交修改文件的统计信息，每个提交都列出了修改过的文件，以及其中添加和移除的行数，并在最后列出所有增减行数小计。

3. `--shortstat`：只显示`--stat`中最后的行数添加、修改、删除的统计。

4. `--name-only`：仅在提交信息后，显示已修改的文件清单。

5. `--name-status`：显示新增、修改、删除的文件清单。

6. `--abbrev-commit`：仅显示`SHA-1`校验和的前几个字符，而非所有的40个字符。

7. `--relative-date`：使用较短的相对时间，而不是完整格式显示日期（比如`“2 weeks ago”`）。

8. `--graph`：在日志旁以`ASCII`图形显示分支与合并历史。

9. `--online`：列表的形式查看历史版本记录，`--pretty=oneline --abbrev-commit` 合用的简写。

10. `--pretty`：使用其他格式显示历史提交信息。可用的选项包括`oneline`、`short`、`full`、`fuller`和 `format`（用来定义自己的格式）。

11. `--decorate`：查看各个分支当前所指的对象。

    > `oneline` 会将每个提交放在一行显示。
    >
    > `format` 可以定制记录的显示格式。
    >
    > ```console
    > $ git log --pretty=format:"%h - %an, %ar : %s"
    > ca82a6d - Scott Chacon, 6 years ago : changed the version number
    > 085bb3b - Scott Chacon, 6 years ago : removed unnecessary test
    > a11bef0 - Scott Chacon, 6 years ago : first commit
    > ```

    | `%H`  | 提交的完整哈希值                              |
    | ----- | --------------------------------------------- |
    | `%h`  | 提交的简写哈希值                              |
    | `%T`  | 树的完整哈希值                                |
    | `%t`  | 树的简写哈希值                                |
    | `%P`  | 父提交的完整哈希值                            |
    | `%p`  | 父提交的简写哈希值                            |
    | `%an` | 作者名字                                      |
    | `%ae` | 作者的电子邮件地址                            |
    | `%ad` | 作者修订日期（可以用 --date=选项 来定制格式） |
    | `%ar` | 作者修订日期，按多久以前的方式显示            |
    | `%cn` | 提交者的名字                                  |
    | `%ce` | 提交者的电子邮件地址                          |
    | `%cd` | 提交日期                                      |
    | `%cr` | 提交日期（距今多长时间）                      |
    | `%s`  | 提交说明                                      |

> *作者* 和 *提交者* 之间的差别： 作者指的是实际作出修改的人，提交者指的是最后将此工作成果提交到仓库的人。



#### 2. 好用的参数

1. `$ git log --pretty=format:"%h %s" --graph`
1. `$ git log --oneline --decorate --graph --all`

TODO

#### 3. 限制输出长度

1. `-n`（比如刚刚的-2）

2. `--since` 和 `--until`

   下面的命令会列出最近两周的所有提交：

   ```console
   $ git log --since=2.weeks
   ```
   该命令可用的格式十分丰富——可以是类似 `"2008-01-15"` 的具体的某一天，也可以是类似 `"2 years 1 day 3 minutes ago"` 的相对日期。

3. `-S`它接受一个字符串参数，并且只会显示那些添加或删除了该字符串的提交。 假设你想找出添加或删除了对某一个特定函数的引用的提交，可以调用：

   ```console
   $ git log -S function_name
   ```

| 选项                  | 说明                                       |
| --------------------- | ------------------------------------------ |
| `-<n>`                | 仅显示最近的 n 条提交。                    |
| `--since`, `--after`  | 仅显示指定时间之后的提交。                 |
| `--until`, `--before` | 仅显示指定时间之前的提交。                 |
| `--author`            | 仅显示作者匹配指定字符串的提交。           |
| `--committer`         | 仅显示提交者匹配指定字符串的提交。         |
| `--grep`              | 仅显示提交说明中包含指定字符串的提交。     |
| `-S`                  | 仅显示添加或删除内容匹配指定字符串的提交。 |

### (10)撤销操作

#### 1. git commit --amend

有时候我们提交完了才发现漏掉了几个文件没有添加，或者提交信息写错了。 此时，可以运行带有 `--amend` 选项的提交命令来重新提交：

` $ git commit --amend`

这个命令会将暂存区中的文件提交。

例如，你提交后发现忘记了暂存某些需要的修改，可以像下面这样操作：

```console
$ git commit -m 'initial commit'
$ git add forgotten_file
$ git commit --amend
```

最终你只会有一个提交——第二次提交将代替第一次提交的结果。

#### 2. 取消暂存的文件(git reset HEAD [file_name])

实际上`git status`提示了你怎么取消暂存：

```console
$ git add *
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    renamed:    README.md -> README
    modified:   CONTRIBUTING.md
```

我们可以这样来取消暂存 `CONTRIBUTING.md` 文件：

```console
$ git reset HEAD CONTRIBUTING.md
Unstaged changes after reset:
M	CONTRIBUTING.md
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    renamed:    README.md -> README

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   CONTRIBUTING.md
```

#### 3. 撤消对文件的修改，使文件回到上一次提交的样子(git checkout --[file_name])

`git status`同样告诉了我们该怎么做：

```console
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   CONTRIBUTING.md
$ git checkout -- CONTRIBUTING.md
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    renamed:    README.md -> README
```

#### 4. git reset

在我看来这才是最强的撤销，但正因为它强，所以他很危险。~~你有可能因为它白干一整天。~~但它救过我狗命，所以在这里写上。

`$ git reset [--soft | --mixed | --hard] [HEAD]`

git reset 命令用于回退版本，可以指定退回某一次提交的版本。



[--soft | --mixed | --hard]：

1. --mixed(默认)：将指定 commit id（也就是校验码） 撤回之后所有内容全部**放进工作区**中。

   ```console
   # 回退到指定 commit id 并且将回退的代码全部放入到工作区中。
   $ git reset --mixed c983d4f8da9ab3b8db3d84d2d53e14c56047abc7
   # 或
   $ git reset c983d4f8da9ab3b8db3d84d2d53e14c56047abc7
   ```

2. --soft：将指定 commit id 撤回之后所有内容全部**放进暂存区**。

3. --hard(慎用)：将指定 commit id 撤回并**清空工作目录及暂存区所有修改**。



[HEAD]：

- HEAD 表示当前版本
- HEAD^ 上一个版本
- HEAD^^ 上上一个版本
- 以此类推...
- 
- HEAD~0 表示当前版本
- HEAD~1 上一个版本
- HEAD^2 上上一个版本
- 以此类推...
- 
- 指定的commit id（校验码），将当前分支（也就是HEAD指针指向的）退回到指定的commit版本上。其实不需要把整段校验码都复制上去，Git会根据前几位自己找。


### (11) 打标签(git tag)

因为不常用，所以之后有空再写。

TODO

### (12) 别名(git config --global)

因为不常用，所以之后有空再写。

TODO

 

## 3. 远程仓库的使用 (git remote)

`$ git remote`

会列出你指定的每一个远程服务器的简写。如果你已经克隆了自己的仓库，那么至少应该能看到 origin ——这是 Git 给你克隆的仓库服务器的默认名字：

```console
$ git clone https://github.com/schacon/ticgit
Cloning into 'ticgit'...
remote: Reusing existing pack: 1857, done.
remote: Total 1857 (delta 0), reused 0 (delta 0)
Receiving objects: 100% (1857/1857), 374.35 KiB | 268.00 KiB/s, done.
Resolving deltas: 100% (772/772), done.
Checking connectivity... done.
$ cd ticgit
$ git remote
origin
```

`$ git remote -v`会显示需要读写远程仓库使用的 Git 保存的简写与其对应的 URL。

```console
$ git remote -v
origin	https://github.com/schacon/ticgit (fetch)
origin	https://github.com/schacon/ticgit (push)
```

### (1) 添加远程仓库(git remote add)

`git remote add <shortname> <url>`

添加一个新的远程 Git 仓库，同时指定一个方便使用的简写：

```console
$ git remote
origin
$ git remote add pb https://github.com/paulboone/ticgit
$ git remote -v
origin	https://github.com/schacon/ticgit (fetch)
origin	https://github.com/schacon/ticgit (push)
qb	https://github.com/paulboone/ticgit (fetch)
qb	https://github.com/paulboone/ticgit (push)
```

现在你可以在命令行中使用字符串 `pb` 来代替整个 URL。比如可以运行 `git fetch pb`：

```console
$ git fetch pb
remote: Counting objects: 43, done.
remote: Compressing objects: 100% (36/36), done.
remote: Total 43 (delta 10), reused 31 (delta 5)
Unpacking objects: 100% (43/43), done.
From https://github.com/paulboone/ticgit
 * [new branch]      master     -> pb/master
 * [new branch]      ticgit     -> pb/ticgit
```

### (2) 从仓库fetch和pull(git fetch/git pull)

从远程仓库中获得数据，可以执行：

`$ git fetch <remote>`

`git fetch` 命令只会将数据下载到你的本地仓库——它并不会自动合并或修改你当前的工作。你必须手动合并这些工作。

`$ git pull <remote>`

`git pull`	=	`git fetch`	+	`git merge`

### (3) 推送到远程仓库(git push)

`$git push <remote> <branch>`

比如你想把master分支推送到origin服务器上，就可以输入：

` $ git push origin master`

在这里不太好继续说，得先介绍分支后才能继续讲。

### (4) 查看某个远程仓库(git remote show)

`$ git remote show <remote> `

可以查看某一个远程仓库的更多信息：

```console
$ git remote show origin
* remote origin
  Fetch URL: https://github.com/schacon/ticgit
  Push  URL: https://github.com/schacon/ticgit
  HEAD branch: master
  Remote branches:
    master                               tracked
    dev-branch                           tracked
  Local branch configured for 'git pull':
    master merges with remote master
  Local ref configured for 'git push':
    master pushes to master (up to date)
```

### (5) 远程仓库的重命名与移除(git remote rename和git remote rm)

`$ git remote rename`

```console
$ git remote rename qb paul
$ git remote
origin
paul
```

`$ git remote rm`

一旦你使用这种方式删除了一个远程仓库，那么所有和这个远程仓库相关的远程跟踪分支以及配置信息也会一起被删除。

```console
$ git remote remove paul
$ git remote
origin
```



## 4. Git分支

终于来到了Git最牛逼的地方了，正是因为Git处理分支的方式难以想象的轻量，所以它才能在众多版本控制系统中脱颖而出。

虽然说本笔记不会包含太多原理上的知识，但是在这里必须说一些原理，才能理解分支到底在干什么。

### (1) Git是如何保存数据的

Git 保存的不是文件的变化或者差异，而是一系列不同时刻的**快照** 。

在进行提交操作时，Git 会保存一个提交对象（commit object）。 该提交对象会包含一个指向暂存内容快照的指针。该提交对象还包含了作者的姓名和邮箱、提交时输入的信息以及指向它的父对象的指针。

 首次提交产生的提交对象没有父对象，普通提交操作产生的提交对象有一个父对象， 而由多个分支合并产生的提交对象有多个父对象.



举个例子，我们假设现在有一个工作目录，里面包含了三个将要被暂存和提交的文件。

暂存操作(git add)会**为每一个文件计算校验和**，然后会把当前版本的文件快照保存到 Git 仓库中 （Git 使用 *blob* 对象来保存它们），最终将校验和加入到暂存区域等待提交：

```console
$ git add README test.rb LICENSE
$ git commit -m 'The initial commit of my project'
```

当使用 `git commit` 进行提交操作时，Git 会先计算**每一个子目录的校验和**（本例中只有项目根目录）。 然后在 Git 仓库中这些校验和保存为树对象。

随后，Git 便会创建一个提交对象， 它除了包含上面提到的那些信息外，还包含指向这个树对象（项目根目录）的指针。 如此一来，Git 就可以在需要的时候重现此次保存的快照。

以下对象里：

白色：提交对象

蓝色：树对象（给项目目录的）

黄色：blob 对象（给项目文件的）

<img src="http://salieri-typora.oss-cn-shanghai.aliyuncs.com/img/markdown/commit-and-tree.png" alt="首次提交对象及其树结构。" style="zoom: 80%;" />



做些修改后再次提交，那么这次产生的提交对象会包含一个指向上次提交对象（父对象）的指针。

<img src="http://salieri-typora.oss-cn-shanghai.aliyuncs.com/img/markdown/commits-and-parents.png" alt="提交对象及其父对象。" style="zoom:80%;" />



Git 的分支，其实本质上仅仅是指向提交对象的可变指针。 

Git 的默认分支名字是 `master`。

在多次提交操作之后，你其实已经有一个指向最后那个提交对象的 `master` 分支。 `master` 分支会在每次提交时自动向前移动。

<img src="http://salieri-typora.oss-cn-shanghai.aliyuncs.com/img/markdown/branch-and-history.png" alt="分支及其提交历史。" style="zoom:80%;" />

### (2) 分支创建(git branch [branch_name])

`$ git branch [branch_name]`

比如，创建一个 testing 分支：

`$ git branch testing`

这会在**当前所在的提交对象**上创建一个指针。

<img src="http://salieri-typora.oss-cn-shanghai.aliyuncs.com/img/markdown/two-branches.png" alt="两个指向相同提交历史的分支。" style="zoom:80%;" />



那么，Git 又是怎么知道当前在哪一个分支上呢？ 也很简单，它有一个名为 `HEAD` 的特殊指针。

`HEAD`是一个指针，**指向当前所在的本地分支**（可以将 `HEAD` 想象为当前分支的别名，是指针的指针）。 在本例中，你仍然在 `master` 分支上。 因为 `git branch` 命令仅仅 **创建** 一个新分支，并不会自动切换到新分支中去。

<img src="http://salieri-typora.oss-cn-shanghai.aliyuncs.com/img/markdown/head-to-master.png" alt="HEAD 指向当前所在的分支。" style="zoom:80%;" />

### (3) 分支切换(git checkout [branch_name])

`$ git checkout [branch_name]`

`$ git checkout -b [branch_name]`：创建一个叫[branch_name]的分支，并且切换到这条分支上。

要切换到一个已存在的分支，你需要使用 `git checkout` 命令。 我们现在切换到新创建的 `testing` 分支去：

`$ git checkout testing`

这样 `HEAD` 就指向 `testing` 分支了。

<img src="http://salieri-typora.oss-cn-shanghai.aliyuncs.com/img/markdown/head-to-testing.png" alt="HEAD 指向当前所在的分支。" style="zoom:80%;" />

如果在这时，我们再提交一次：

` $ git commit -a -m 'made a change'`

<img src="http://salieri-typora.oss-cn-shanghai.aliyuncs.com/img/markdown/advance-testing.png" alt="HEAD 分支随着提交操作自动向前移动。" style="zoom:80%;" />

如图所示，你的 `testing` 分支向前移动了，但是 `master` 分支却没有，它仍然指向运行 `git checkout` 时所指的对象。

现在我们切换回 `master` 分支看看：

`$ git checkout master`

<img src="http://salieri-typora.oss-cn-shanghai.aliyuncs.com/img/markdown/checkout-master.png" alt="检出时 HEAD 随之移动。" style="zoom:80%;" />

这条命令做了两件事。

1. HEAD 指回 `master` 分支
2.  将工作目录恢复成 `master` 分支所指向的快照内容。 



再做点修改并且提交：

`$ git commit -a -m 'made other changes'`

<img src="http://salieri-typora.oss-cn-shanghai.aliyuncs.com/img/markdown/advance-master.png" alt="项目分叉历史。" style="zoom:80%;" />

现在，这个项目的提交历史已经产生了分叉，你可以在不同分支间不断地来回切换和工作，并在时机成熟时将它们合并起来。

### (4) 分支删除(git branch -d [branch_name])

`$ git branch -d [branch_name]`

此时你可以把testing这个分支删掉，通过：

`$git branch -d testing`

但是这不代表你的记录或者工作内容就消失了，你只是删掉了一个指向快照的指针。你可以通过`git reset`回到`87ab2`处，继续工作并且创建一个新的分支。

### (5) 分支合并(git merge)（方案一）

`$ git merge`

假设你现在的提交记录是这样的：

<img src="http://salieri-typora.oss-cn-shanghai.aliyuncs.com/img/markdown/basic-branching-4.png" alt="基于 `master` 分支的紧急问题分支（hotfix branch）。" style="zoom:80%;" />

假如现在你在hotfix上的工作搞完了，想要把master和hotfix合并起来：

```console
$ git checkout master
$ git merge hotfix
Updating f42c576..3a0874c
Fast-forward
 index.html | 2 ++
 1 file changed, 2 insertions(+)
```

这是一个fast-forward，因为你想要合并的分支 `hotfix` 所指向的提交 `C4` 是你所在的提交 `C2` 的直接后继，因此 Git 会直接将指针向前移动。这种情况下的合并操作没有需要解决的分歧——这就叫做 “快进（fast-forward）”。

<img src="http://salieri-typora.oss-cn-shanghai.aliyuncs.com/img/markdown/basic-branching-5.png" alt="`master` 被快进到 `hotfix`。" style="zoom:80%;" />

然后你把hotfix这个分支删掉了:

`$ git branch -d hotfix`

<img src="http://salieri-typora.oss-cn-shanghai.aliyuncs.com/img/markdown/basic-branching-6.png" alt="继续在 `iss53` 分支上的工作。" style="zoom:80%;" />

假如现在你在iss53上的工作搞完了，想要把master和iss53合并起来：

```console
$ git checkout master
Switched to branch 'master'
$ git merge iss53
Merge made by the 'recursive' strategy.
index.html |    1 +
1 file changed, 1 insertion(+)
```

这和你之前合并 `hotfix` 分支的时候看起来有一点不一样。 

在这种情况下，你的开发历史从一个更早的地方开始分叉开来（diverged）。 因为，`master` 分支所在提交并不是 `iss53` 分支所在提交的直接祖先。

出现这种情况的时候，Git 会使用两个分支的末端所指的快照（`C4` 和 `C5`）以及这两个分支的公共祖先（`C2`），做一个简单的**三方合并**。

<img src="http://salieri-typora.oss-cn-shanghai.aliyuncs.com/img/markdown/basic-merging-1.png" alt="一次典型合并中所用到的三个快照。" style="zoom:80%;" />

Git 将此次三方合并的结果**做了一个新的快照并且自动创建一个新的提交指向它**。 这个被称作一次合并提交，它的特别之处在于他有不止一个父提交。

<img src="http://salieri-typora.oss-cn-shanghai.aliyuncs.com/img/markdown/basic-merging-2.png" alt="一个合并提交。" style="zoom:80%;" />

#### 1. 遇到冲突时的分支合并

如果你在`maste`r分支的修改和有关 `hotfix` 分支的修改都涉及到同一个文件的同一处，在合并它们的时候就会产生合并冲突：

```console
$ git merge iss53
Auto-merging index.html
CONFLICT (content): Merge conflict in index.html
Automatic merge failed; fix conflicts and then commit the result.
```

此时 Git 做了合并，但是没有自动地创建一个新的合并提交。 Git 会暂停下来，等待你去解决合并产生的冲突。

 你可以在合并冲突后的任意时刻使用 `git status` 命令来查看那些因包含合并冲突而处于未合并（unmerged）状态的文件：

```console
$ git status
On branch master
You have unmerged paths.
  (fix conflicts and run "git commit")

Unmerged paths:
  (use "git add <file>..." to mark resolution)

    both modified:      index.html

no changes added to commit (use "git add" and/or "git commit -a")
```

你可以**自己手动打开**这些包含冲突的文件然后**手动解决**冲突。 出现冲突的文件会包含一些特殊区段，看起来像下面这个样子：

```console
<<<<<<< HEAD:index.html
<div id="footer">contact : email.support@github.com</div>
=======
<div id="footer">
 please contact us at support@github.com
</div>
>>>>>>> iss53:index.html
```

这表示 `HEAD` 所指示的版本（也就是你的 `master` 分支所在的位置）在这个区段的上半部分（`=======` 的上半部分）

而 `iss53` 分支所指示的版本在 `=======` 的下半部分。 

为了解决冲突，你必须选择使用由 `=======` 分割的两部分中的一个，或者你也可以自行合并这些内容。



在你解决了所有文件里的冲突之后，对每个文件使用 `git add` 命令来将其标记为**冲突已解决**。 一旦暂存这些原本有冲突的文件，Git 就会将它们标记为冲突已解决。

##### 冲突的图形化解决工具

你可以运行 `git mergetool`，该命令会为你启动一个合适的可视化合并工具，并带领你一步一步解决这些冲突。

我个人觉得不好用，所以在这里不多写了。



### (6) 分支变基(git rebase)（方案二）

`$ git rebase`

在 Git 中整合来自不同分支的修改主要有两种方法：`merge` 以及 `rebase`。

各有各的好处吧，~~虽然说我不太喜欢用~~



假如你现在的提交记录是这样的：

<img src="http://salieri-typora.oss-cn-shanghai.aliyuncs.com/img/markdown/basic-rebase-1.png" alt="分叉的提交历史。" style="zoom:80%;" />

如果你用`$ git merge`合并，会变成这样（它会进行三方合并）：

<img src="http://salieri-typora.oss-cn-shanghai.aliyuncs.com/img/markdown/basic-rebase-2.png" alt="通过合并操作来整合分叉了的历史。" style="zoom:80%;" />

但如果你用`$ git rebase`：

```console
$ git checkout experiment
$ git rebase master
First, rewinding head to replay your work on top of it...
Applying: added staged command
```

将提交到某一分支上的所有修改都移至另一分支上，就好像“重新播放”一样。

<img src="http://salieri-typora.oss-cn-shanghai.aliyuncs.com/img/markdown/basic-rebase-3.png" alt="将 `C4` 中的修改变基到 `C3` 上。" style="zoom:80%;" />

此时，`C4'` 指向的快照就和上面的例子中的`C5` 指向的快照一模一样了。 这两种整合方法的**最终结果没有任何区别**，但是变基使得**提交历史更加整洁**。

你在查看一个经过变基的分支的历史记录时会发现，尽管实际的开发工作是并行的， 但它们看上去就像是串行的一样，提交历史是一条直线没有分叉。



以下是个人观点：

> 我认为，提交历史就得记录实际上都发生了什么，变基实际上遮盖了实际发生的事情，让有用的信息变少了。如果你用的不好，很有可能会坑到一个项目的同事，而merge就不会有这种情况。



如果你记不住`git merge`或`git rebase`后，你的哪个分支会往前移：
记住，会改变的永远是你所在的分支，其他的分支不会改变。



### (7) 分支开发工作流

#### 1. 长期分支

因为 Git 使用简单的三方合并，所以就算在一段较长的时间内，反复把一个分支合并入另一个分支，也不是什么难事。 

许多使用 Git 的开发者都喜欢使用这种方式来工作，比如只在 `master` 分支上保留完全稳定的代码——有可能仅仅是已经发布或即将发布的代码。 

他们还有一些名为 `develop` 或者 `next` 的平行分支，被用来做后续开发或者测试稳定性——这些分支不必保持绝对稳定，但是一旦达到稳定状态，它们就可以被合并入 `master` 分支了。

<img src="http://salieri-typora.oss-cn-shanghai.aliyuncs.com/img/markdown/lr-branches-1.png" alt="趋于稳定分支的线性图。" style="zoom:80%;" />

#### 2. 主题分支

考虑这样一个例子，你在 `master` 分支上工作到 `C1`，这时为了解决一个问题而新建 `iss91` 分支，在 `iss91` 分支上工作到 `C4`，然而对于那个问题你又有了新的想法，于是你再新建一个 `iss91v2` 分支试图用另一种方法解决那个问题，接着你回到 `master` 分支工作了一会儿，你又冒出了一个不太确定的想法，你便在 `C10` 的时候新建一个 `dumbidea` 分支，并在上面做些实验。 你的提交历史看起来像下面这个样子：

<img src="http://salieri-typora.oss-cn-shanghai.aliyuncs.com/img/markdown/topic-branches-1.png" alt="拥有多个主题分支的提交历史。" style="zoom:80%;" />

### (8) 远程分支

远程引用是对远程仓库的引用（指针），包括分支、标签等等。

 `$ git ls-remote <remote>` 来显式地获得远程引用的完整列表

 `$ git remote show <remote>` 获得远程分支的更多信息

远程跟踪分支是远程分支状态的引用。它们是你无法移动的本地引用。一旦你进行了网络通信， Git 就会为你移动它们以精确反映远程仓库的状态。

它们以 `<remote>/<branch>` 的形式命名。 

例如，如果你想要看你最后一次与远程仓库 `origin` 通信时 `master` 分支的状态，你可以查看 `origin/master` 分支。

假设你的网络里有一个在 `git.ourcompany.com` 的 Git 服务器。 
如果你从这里克隆，Git 的 `clone` 命令会为你自动将其命名为 `origin`，拉取它的所有数据， 创建一个指向它的 `master` 分支的指针，并且在本地将其命名为 `origin/master`。 
Git 也会给你一个与 origin 的 `master` 分支在指向同一个地方的本地 `master` 分支：

<img src="http://salieri-typora.oss-cn-shanghai.aliyuncs.com/img/markdown/remote-branches-1.png" alt="克隆之后的服务器与本地仓库。" style="zoom:80%;" />

如果你在本地的 `master` 分支做了一些工作，在同一段时间内有其他人推送提交到 `git.ourcompany.com` 并且更新了它的 `master` 分支，这就是说你们的提交历史已走向不同的方向。
 即便这样，只要你保持不与 `origin` 服务器连接（并拉取数据），你的 `origin/master` 指针就不会移动。

<img src="http://salieri-typora.oss-cn-shanghai.aliyuncs.com/img/markdown/remote-branches-2.png" alt="本地与远程的工作可以分叉。" style="zoom:80%;" />

如果要与给定的远程仓库同步数据，运行 `git fetch <remote>` 命令（在本例中为 `git fetch origin`）。（如果是`git pull`的话，还会帮你merge一下）

<img src="http://salieri-typora.oss-cn-shanghai.aliyuncs.com/img/markdown/remote-branches-3.png" alt="`git fetch` 更新你的远程仓库引用。" style="zoom:80%;" />

假定你有另一个内部 Git 服务器。 这个服务器位于 `git.team1.ourcompany.com`。 
你可以运行 `git remote add` 命令添加一个新的远程仓库引用到当前的项目。 将这个远程仓库命名为 `teamone`，将其作为完整 URL 的缩写。可以运行 `git fetch teamone` 来抓取远程仓库 `teamone` 有而本地没有的数据。 

<img src="http://salieri-typora.oss-cn-shanghai.aliyuncs.com/img/markdown/remote-branches-5.png" alt="远程跟踪分支 `teamone/master`。" style="zoom:80%;" />



#### 1. 推送(git push [remote] [branch])

`$git push [remote] [branch]`

当你想要公开分享一个分支时，需要将其推送到**有写入权限**的远程仓库上。

 本地的分支并不会自动与远程仓库同步——你必须显式地推送想要分享的分支。

如果希望和别人一起在名为 `serverfix` 的分支上工作，你可以像推送第一个分支那样推送它。 运行 `git push origin serverfix`:

```console
$ git push origin serverfix
Counting objects: 24, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (15/15), done.
Writing objects: 100% (24/24), 1.91 KiB | 0 bytes/s, done.
Total 24 (delta 2), reused 0 (delta 0)
To https://github.com/schacon/simplegit
 * [new branch]      serverfix -> serverfix
```

这意味着：“推送本地的 `serverfix` 分支来更新远程仓库上的 `serverfix` 分支，如果远程没有叫 `serverfix`的分支，那么就会自动创建一个，然后再push上去。”

==注意：==即便**没有定义**一个本地分支的上游分支，Git **仍然会**尝试将当前分支的更改推送到与其同名的远程分支（如果存在的话）

可以运行 `git push origin serverfix:serverfix`， 它会做同样的事

可以运行 `git push origin serverfix:awesomebranch` 来将本地的 `serverfix` 分支推送到远程仓库上的 `awesomebranch` 分支。



下一次其他协作者从服务器上抓取数据时，他们会在本地生成一个远程分支 `origin/serverfix`，指向服务器的 `serverfix` 分支的引用：

```console
$ git fetch origin
remote: Counting objects: 7, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 3 (delta 0)
Unpacking objects: 100% (3/3), done.
From https://github.com/schacon/simplegit
 * [new branch]      serverfix    -> origin/serverfix
```

==注意：==这种情况下，不会有一个新的 `serverfix` 分支——只有一个不可以修改的 `origin/serverfix` 指针。
也就是说，如果你需要在这个分支上继续编辑的话，需要自己创建一个新的分支。



#### 2. 跟踪分支(git checkout / -u / --set-upstream-to)

从一个远程跟踪分支检出一个本地分支会自动创建所谓的“**跟踪分支**”（它跟踪的分支叫做“**上游分支**”）。 
跟踪分支是与远程分支有直接关系的本地分支。 如果在一个跟踪分支上输入 `git pull`，**Git 能自动地识别去哪个服务器上抓取、合并到哪个分支,也能自动识别要push到哪个服务器的哪个分支**。

当克隆一个仓库时，它通常会自动地创建一个跟踪 `origin/master` 的 `master` 分支。

`$ git checkout -b [branch] [remote]/[branch]`设置跟踪分支。

 这是一个十分常用的操作所以 Git 提供了 `--track` 快捷方式：

```console
$ git checkout --track origin/serverfix
Branch serverfix set up to track remote branch serverfix from origin.
Switched to a new branch 'serverfix'
```

由于这个操作太常用了，该捷径本身还有一个捷径。 如果你尝试检出的分支 (a) 不存在且 (b) 刚好只有一个名字与之匹配的远程分支，那么 Git 就会为你创建一个跟踪分支：

```console
$ git checkout serverfix
Branch serverfix set up to track remote branch serverfix from origin.
Switched to a new branch 'serverfix'
```

当然，如果要设置不一样的名字，那只能用`$ git checkout -b [branch] [remote]/[branch]`设置。

 你可以在任意时间使用 `-u` 或 `--set-upstream-to` 选项运行 `git branch` 来显式地设置。（比如github在帮你初始化本地仓库的时候就会推荐你运行`git push -u [branch] [remote]/[branch]`

```console
$ git branch -u origin/serverfix
Branch serverfix set up to track remote branch serverfix from origin.
```



`$ git branch -vv`：查看设置的所有跟踪分支

```console
$ git branch -vv
  iss53     7e424c3 [origin/iss53: ahead 2] forgot the brackets
  master    1ae2a45 [origin/master] deploying index fix
* serverfix f8674d9 [teamone/server-fix-good: ahead 3, behind 1] this should do it
  testing   5ea463a trying something new
```

> `iss53` 分支正在跟踪 `origin/iss53` 并且 “ahead” 是 2，意味着本地有两个提交还没有推送到服务器上。
>
>   `master` 分支正在跟踪 `origin/master` 分支并且是最新的。 
>
>  `serverfix` 分支正在跟踪 `teamone` 服务器上的 `server-fix-good` 分支并且领先 3 落后 1， 意味着服务器上有一次提交还没有合并入，同时本地有三次提交还没有推送。
>
>  最后看到 `testing` 分支并没有跟踪任何远程分支。



#### 3. 删除远程分支(git push [remote] --delete [branch_name])

`$ git push [remote] -- delete [branch_name]`：

如果想要从服务器上删除 `serverfix` 分支，运行下面的命令：

```console
$ git push origin --delete serverfix
To https://github.com/schacon/simplegit
 - [deleted]         serverfix
```

基本上这个命令做的只是从服务器上移除这个指针。 Git 服务器通常会保留数据一段时间直到垃圾回收运行，所以如果不小心删除掉了，通常是很容易恢复的。



## 5.分布式工作(以github为例)

有些项目需要很多人一起协作完成，如何使这个过程尽可能可靠且方便？

流程通常如下：

1. 派生一个项目
2. 从 `master` 分支创建一个新分支
3. 提交一些修改来改进项目
4. 将这个分支推送到 GitHub 上
5. 创建一个拉取请求
6. 讨论，根据实际情况继续修改
7. 项目的拥有者合并或关闭你的拉取请求
8. 将更新后的 `master` 分支同步到你的派生中

### (1) 对项目做出贡献

#### 1. 派生项目(fork)

如果你想要参与某个项目，但是并没有推送权限，这时可以对这个项目进行“派生（Fork）”。 当你“派生”一个项目时，GitHub 会在你的空间中创建一个完全属于你的项目副本，且你对其具有推送权限。

通过这种方式，项目的管理者不再需要忙着把用户添加到贡献者列表并给予他们推送权限。 
人们可以派生这个项目，将修改推送到派生出的项目副本中，并通过创建拉取请求（Pull Request，简称 PR）来让他们的改动进入源版本库，下文我们会详细说明。 
创建了拉取请求后，就会开启一个可供审查代码的板块，项目的拥有者和贡献者可以在此讨论相关修改，直到项目拥有者对其感到满意，并且认为这些修改可以被合并到版本库。

按这个按钮来派生这个项目：

<img src="http://salieri-typora.oss-cn-shanghai.aliyuncs.com/img/markdown/forkbutton.png" alt="“Fork”按钮." style="zoom:80%;" />



#### 2/3.在本地创建分支/在本地修改代码

你可以在本地额外创建一个分支进行工作，然后进行代码编写，来推进项目。



#### 4. 将本地修改推送到自己fork下来的github仓库里

`git push`即可。



#### 5. 创建拉取请求(pull request)

现在到 GitHub 上查看之前的项目副本，可以看到 GitHub 提示我们有新的分支， 并且显示了一个大大的绿色按钮让我们可以检查我们的改动，并给源项目创建拉取请求。

<img src="http://salieri-typora.oss-cn-shanghai.aliyuncs.com/img/markdown/blink-02-pr.png" alt="拉取请求按钮" style="zoom:80%;" />

如果我们点击那个绿色按钮，就会跳到一个新页面，在这里我们可以为拉取请求填写标题和描述。 花点时间编写一个清晰有用的描述是非常值得的，这能让原项目拥有者明白你做了什么， 为什么这个改动是正确的，以及接受此更改是否能够改进他的项目。

同时我们也能看到比主分支中所“领先”（ahead）的提交（在这个例子中只有一个）以及所有将会被合并的改动与之前代码的对比。

<img src="http://salieri-typora.oss-cn-shanghai.aliyuncs.com/img/markdown/blink-03-pull-request-open.png" alt="拉取请求创建页面" style="zoom:80%;" />

当你单击了“Create pull request”（创建拉取请求）的按钮后，这个项目的拥有者将会收到一条包含关改动和拉取请求页面的链接的提醒。（一般而言通过邮件）

#### 6. 交流讨论、继续修改

现在，项目的拥有者可以看到你的改动并合并它，拒绝它或是发表评论。

接下来可能会通过电子邮件进行互动，但是在 GitHub，这些都可以在线上完成。

<img src="http://salieri-typora.oss-cn-shanghai.aliyuncs.com/img/markdown/blink-05-general-comment.png" alt="拉取请求讨论页面" style="zoom:80%;" />

#### 7. 维护者同意或拒绝请求

维护者觉得OK就可以同意merge。



### (2) 维护项目

如果要用pull request来维护的话，其实大致都在上面写好了。

如果你是和一些很信任的同伴共同完成某个项目的话，也可以不用这么麻烦，直接在github里给他们仓库写入权限就好了。

