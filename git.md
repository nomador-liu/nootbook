# github

## git图形化界面操作

建立文件hello.py

```python
print('hello')
```

点击git菜单选择提交，写点说明提交即可。


我们用了git之后，它默认就有一个分支叫做主分支。master/main

创建新分支：
分支：原有内容做一个拷贝


我们在v1里面改了原来的hello.py，也添加了新的v1_new文件
提交‼️‼️‼️


提交
新建立分支
切换分支

合并分支：v1合并到main/master
1. 先切回主分支
2. 将v1合并进来


任何一个git项目，在项目的根目录下一定包含一个.git文件夹。

电脑上的git软件有一个叫做.gitconfig的配置文件，这个文件放在主目录下
这个配置文件中必不可少的就是用户名和邮箱

‼️‼️时刻注意命令行你所在的位置

## git 综合案例

### case1

八、综合案例
例子1:

初始化仓库 → 新建分支开发 → 提交 → 合并 → rebase → 回滚 → 暂存 → 查看历史

- 初始化仓库

```bash
mkdir git-demo && cd git-demo
git init
echo "Hello" > app.txt
git add app.txt
git commit -m "init: first commit"
git status
```

- 新建立分支开发

```bash
git switch -c feature/login  # 建立新分支feature/login并切换过去
```

修改代码：

```bash
echo "add login page" >> app.txt  # 在app.txt中添加一行add login page 这里只为模拟这个文件被修改
git add .  # .表示当前位置下一切内容都被git管理
git commit -m "feat(login): add login page"
```

- 同时main也有新提交

```bash
git switch main  # 切换回main分支
echo "update readme" > README.md   # 新建立文件
git add README.md
git commit -m "docs: add readme"
```



- 合并（merge）方式

```bash
git merge feature/login  # 会产生一个合并提交。
```

我们在main添加了新内容，然后感觉分支feature/login对老内容的修改是我们需要的，所以将其合并到main中来。
所以此时的main既有新添加的内容，也有新分支中修改后的内容。

合并的时候出现一个窗口

```bash
Merge branch 'feature/login'

# Please enter a commit message to explain why this merge is necessary,

# especially if it merges an updated upstream into a topic branch.

#

# Lines starting with '#' will be ignored, and an empty message aborts

# the commit.

～
```

～
～这个窗口是一个提示窗口，我们只需要将其关闭。
多按几次Esc，再按:wq 回车

- 回滚错误提交
  假设刚才改错了一次：

```bash
echo "wrong code" >> app.txt
git add app.txt
git commit -m "feat(login): wrong code"
```

发现有问题：

```bash
git reset HEAD~1 
```

- 回滚错误提交
  假设刚才改错了一次：

```bash
echo "wrong code" >> app.txt
git add app.txt
git commit -m "feat(login): wrong code"
```

发现有问题：

```bash
git reset --hard HEAD~1       # 撤掉提交，保留改动
```

HEAD是什么？
在Git里，HEAD表示：
当前所在分支的最新提交，也就是当前代码快照。可以理解为：
	它是一个“指针”，指向当前分支的最新提交；
	每次 commit，HEAD都会随之往前移动一格。

- 临时保存（stash）—— 在一个分支中做的没提交的操作先保存起来，在另一个分支中恢复这些操作再提交

```bash
echo "git stash" >> app.txt  # 重点是我不提交就要切换分支
git stash  					 # 将刚刚做的操作记录下来
git switch feature/login     # 切到另一个分支
git stash pop  				 # 弹出来	，早期分支中没提交的那个内容就被弹出到这个新分支了。
git commit -am "git stash pop"
```

- 出现冲突时
  文件内容不一致，git无法决定保留谁。

```bash
echo "main\nhello3\n" > hello3.txt  # 确保在main分支中，建立新文件hello.txt
git add hello3.txt
git commit -m "hello3.txt"
git switch -c v3
```

在新分支中将文件hello3.txt改的面目全非

```bash
vim hello3.txt  文件内容如下
v3
hello3
new v3

git add hello3.txt
git commit -m "v3: hello.txt"
git switch main
git merge v3
```

此时会产生冲突，出现一个新的界面如下：

```bash
<<<<<<< HEAD       # 当前分支中文件的内容
main
hello3
=======            # 分隔符
v3
hello3
new v3

>>>>>>> v3         # 另一个分支中文件的呢绒


```

这个新界面可能用vi或者nano打开的。

此时机器的意思就是它没办法帮你决定合并分支后保留哪些内容。
此时，我们直接修改这个文件变成你想要的样子再保存即可。

```bash
git add hello3.txt    # ‼️
git merge --continue  # 刚才合并的时候因为不知道如何处理文件，现在改好了继续合并即可。


```



### case2

例子2: 

一、初始化场景——在桌面新建练习仓，准备好分支、冲突与“隐藏 bug”：



```bash
mkdir -p ~/Desktop/git-lab && cd ~/Desktop/git-lab
git init
git config user.name "Tom"
git config user.email "tom@google.com"
```
初始提交
```bash
printf "1\n2\n3\n" > numbers.txt
echo "Hello" > app.txt
git add .
git commit -m "init: numbers + app"
```

再来两次提交
```bash
echo "4" >> numbers.txt
git commit -am "feat(numbers): append 4"
```

-a 代表自动将所有已被追踪（tracked）的修改文件添加到暂存区。它不会添加新的未追踪文件（即你刚创建但还没用git add过的文件）。

-m 后面跟引号包裹的字符串，表示提交说明。否则git会打开默认编辑器（如vim）让你输入说明。
```bash
echo world >> app.txt
git commit -am "feat(app): say world"
git tag v1          # 记录“已知良好”版本
```


制造一个“bug”提交（假设这里将world改坏）
```bash
sed -i '' 's/world/wrold/' app.txt
git commit -am "bug(app): typo 'world' -> 'wrold'"
```

再加两个普通提交，掩盖 bug
```bash
date +%s >> build.txt
git add build.txt
git commit -m "chore(build): add build stamp"

echo "readme line A" > README.md
git add README.md
git commit -m "docs: add README line A"
```

准备一个会冲突的分支
```bash
git switch -c feature/refactor
```
创建并切换到一个新的分支feature/refactor。-c参数是create，早期写成git checkout -b feature/refactor

此时可以使用git status看看状态
```bash
sed -i '' 's/Hello/Hi/' app.txt
sed -i '' 's/readme line A/readme line FEATURE/' README.md
git commit -am "refactor(app): greet Hi; docs tweak"
```
回到 main，改同一行以制造冲突
```bash
git switch -  # 切换回上一个分支，可以通过git branch查看所有分支
git switch main
sed -i '' 's/readme line A/readme line MAIN/' README.md
git commit -am "docs: tweak README line on main"
```
展示历史
```bash
git log --oneline --graph --decorate --all --max-count=12
```
```bash
git log 查看提交历史

--oneline 		每个提交显示一行（简短哈希 + 提交信息），更紧凑
--graph 		以ASCII图形显示分支和合并的关系（左侧的分支线）
--decorate 		在提交旁边显示分支名、标签等（如 HEAD -> main, origin/main）
--all 			显示所有分支的历史（不只当前分支）
--max-count=12 	只显示最近 12 条提交记录（防止太长）
```
这里我们会得到如下输出
```markdown
* 6474694 (HEAD -> main) docs: tweak README line on main
  | * a108a03 (feature/refactor) refactor(app): greet Hi; docs tweak
  |/  
* ada4a10 docs: add README line A
* b77ded6 chore(build): add build stamp
* b4cbcb2 bug(app): typo 'world' -> 'wrold'
* 21ec949 (tag: v1) feat(app): say world
* 6a2abe9 feat(numbers): append 4
* 5f4bbb5 init: numbers + app
```
下面我们要重点说一下这个输出格式

> feat	:新功能
>
> fix	 :修复，比如修正逻辑或报错
>
> docs	:文档变更修改，比如README、注释
>
> stytle      :代码格式调整，比如改空格、缩进、风格但不改逻辑
>
> refactor    :重构、比如优化代码结构，但不改变行为
>
> perf	:性能优化,比如改进执行效率
>
> test	:测试相关，比如添加或者修改测试代码
>
> chore       :构建、依赖或配置，比如build脚本、依赖等
>
> build       :构建系统相关，比如webpack\makefile\Cmake
>
> ci	  :持续集成相关，比如修改CI配置文件
>
> revert      : 回滚之前的提交，比如撤销错误提交



一、初始化场景——在桌面新建练习仓，准备好分支、冲突与“隐藏 bug”：

```bash
mkdir -p ~/Desktop/git-lab && cd ~/Desktop/git-lab
git init
git config user.name "Tom"
git config user.email "tom@google.com"

# 初始提交

printf "1\n2\n3\n" > numbers.txt
echo "Hello" > app.txt
git add .   # 圆点左侧有空格，含义是将当前文件夹下所有内容添加到git管辖中
git commit -m "init: numbers + app"

# -m后面确实可以随便写，但是一般会用几个特殊字母开头+冒号


# 再来两次提交

echo "4" >> numbers.txt
git commit -am "feat(numbers): append 4"

# -a 代表自动将所有已被追踪（tracked）的修改文件添加到暂存区。它不会添加新的未追踪文件（即你刚创建但还没用git add过的文件）。

# -m 后面跟引号包裹的字符串，表示提交说明。否则git会打开默认编辑器（如vim）让你输入说明。

echo "world" >> app.txt
git commit -am "feat(app): say world"
git tag v1          # 记录“已知良好”版本


# 制造一个“bug”提交（假设这里将world改坏）

sed -i '' 's/world/wrold/' app.txt				# 这是苹果系统下的，在ubuntu的bash里为 sed -i 's/world/wrold/' app.txt
git commit -am "bug(app): typo 'world' -> 'wrold'"

# 再加两个普通提交，掩盖 bug

date +%s >> build.txt
git add build.txt
git commit -m "chore(build): add build stamp"

echo "readme line A" > README.md
git add README.md
git commit -m "docs: add README line A"
```

```bash
git switch -c feature/refactor

sed -i '' 's/Hello/Hi/' app.txt
sed -i '' 's/readme line A/readme line FEATURE/' README.md
git commit -am "refactor(app): greet Hi; docs tweak"


git switch main
sed -i '' 's/readme line A/readme line MAIN/' README.md
git commit -am "docs: tweak README line on main"

git log --oneline --graph --decorate --all --max-count=12
```

下面详细解释

```bash
# 准备一个会冲突的分支

git switch -c feature/refactor

# 创建并切换到一个新的分支feature/refactor。-c参数是create，早期写成git checkout -b feature/refactor

# 此时可以使用git status看看状态

sed -i '' 's/Hello/Hi/' app.txt
sed -i '' 's/readme line A/readme line FEATURE/' README.md
git commit -am "refactor(app): greet Hi; docs tweak"

# 回到 main，改同一行以制造冲突

git switch -  # 切换回上一个分支，可以通过git branch查看所有分支
git switch main
sed -i '' 's/readme line A/readme line MAIN/' README.md
git commit -am "docs: tweak README line on main"

# 展示历史

git log --oneline --graph --decorate --all --max-count=12

# git log 查看提交历史

--oneline 		每个提交显示一行（简短哈希 + 提交信息），更紧凑
--graph 		以ASCII图形显示分支和合并的关系（左侧的分支线）
--decorate 		在提交旁边显示分支名、标签等（如 HEAD -> main, origin/main）
--all 			显示所有分支的历史（不只当前分支）
--max-count=12 	只显示最近 12 条提交记录（防止太长）

# 这里我们会得到如下输出

* 6474694 (HEAD -> main) docs: tweak README line on main
| * a108a03 (feature/refactor) refactor(app): greet Hi; docs tweak
|/  
* ada4a10 docs: add README line A
* b77ded6 chore(build): add build stamp
* b4cbcb2 bug(app): typo 'world' -> 'wrold'
* 21ec949 (tag: v1) feat(app): say world
* 6a2abe9 feat(numbers): append 4
* 5f4bbb5 init: numbers + app
```

  

每一行代表一次提交（commit）。前面的十六进制是提交哈希，可以用git show 数字来显示详细信息。
可以从最后一行向上看，最后一行是第一次操作。
左边的竖线（|、/）和星号（*）表示分支的拓扑结构（谁从谁分出来、哪里合并）。

* 					一个提交（commit）
/ 					分支分叉或合并的节点
HEAD 				当前检出的提交（你现在“停留”的地方）
(HEAD -> main) 		表示你当前在 main 分支，HEAD 指向它
(feature/refactor) 	表示这是另一个分支的最新提交
(tag: v1) 			表示该提交打了一个标签 v1

feat 	 新功能
fix		 修复，比如修正逻辑或报错
docs	 文档变更修改，比如README、注释等
style	 代码格式调整，比如改空格、缩进、风格但不改逻辑
refactor 重构，比如优化代码结构，不改变行为
perf	 性能优化，比如改进执行效率
test	 测试相关，比如添加或修改测试代码
chore	 构建、依赖或配置，比如改 build 脚本、依赖、CI/CD 配置
build	 构建系统相关，比如webpack、makefile、CMake 修改
ci 		 持续集成相关，比如修改 CI 配置文件
revert 	 回滚之前的提交，比如撤销错误提交

括号里的部分叫scope（作用范围），表示改动影响的模块或文件夹。
比如：
feat(numbers): append 4  表示在numbers模块里添加新功能
bug(app): typo 'world' -> 'wrold'  表示app模块里引入了拼写错误
chore(build): add build stamp
scope是可选的但推荐使用，它让你一眼就知道改动发生在哪个部分。
git commit -am "bug(app): typo 'world' -> 'wrold'"
比如这种就是一个例子

到底为止，两个分支中的文件内容已经不一致了（README.md）
昨天我们合并分支的时候冲突出现，今天我们打算rebase的时候产生这个冲突并解决。



## Rebase

Rebase清理和解决冲突——把feature/refactor线性地挪到main最新之上，并解决冲突。

```bash
git switch feature/refactor
git rebase main

# 把你当前分支的提交重新放到main分支的最新提交之后，让历史变得线性、干净。

# 所谓的rebase就是
```



main:    A──B──C
              \
feature:       D──E
将其变为
main:    A──B──C──D'──E'

- 此时git会把你当前分支（比如 feature/refactor）的提交，一个个“重放”到main最新版本之后。

但如果你和main修改了同一个文件的同一部分内容，Git就不知道该保留谁的版本，这时就会产生冲突（conflict）。

- 这里会报冲突（README.md），按提示处理：

```bash
git status

打开 README.md，我们会看到
<<<<<<< HEAD

readme line MAIN
=======

readme line FEATURE

>>>>>>> a108a03 (refactor(app): greet Hi; docs tweak)
>>>>>>> 这个是git生成的内容，此时我们只需要将冲突标记 <<<<<<< … >>>>>>> 合并为你想要的最终内容。
>>>>>>> 至于想编程什么随便我们自己，比如整个文件就变成下面一行
>>>>>>> readme line MAIN+FEATURE
>>>>>>> 然后：

git add README.md
git rebase --continue
```

验证线性历史 & 工作区状态：

```bash
git log --oneline --graph --decorate --all --max-count=12
git status
```

git rebase和git merge 往往是冲突的，merge是团队协作多人开发时用，而rebase更常用于本地开发的个人分支

## 删除本地分支

```bash
git branch -d <分支名>		# 这是合并后可删除

git branch -D <分支名>		# 强制删除，会丢失整个分支未合并的内容
```

## 删除远程分支

```bash
git push origin --delete <分支名>
```

## 清理残留分支

当删除远程分支后，本地可能残留无效跟踪分支，此时可执行

```bash
git fetch -p
# 或
git fetch --prune
```

