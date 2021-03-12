# Git

### 基本命令

````
mkdir wap // 项目在本地的路径
cd wap
git init 
touch README.md 
git add README.md 
git commit -m "first commit" 
git remote add origin https://git.oschina.net/name/package.git  // 远程仓库地址


已有项目：
 
cd existing_git_repo
git remote add origin https://git.oschina.net/name/package.git
git push -u origin master
````



git init 

ll 列出文件

ll -lA

![image-20210309143100051](C:\Users\julie.yang\AppData\Roaming\Typora\typora-user-images\image-20210309143100051.png)

![image-20210308132443205](C:\Users\julie.yang\AppData\Roaming\Typora\typora-user-images\image-20210308132443205.png)

![image-20210308133705279](C:\Users\julie.yang\AppData\Roaming\Typora\typora-user-images\image-20210308133705279.png)

git add  --- add file to commit filed

git add . 将文件夹下所有文件加入到git中

git rm --cached file name, retract the file from commit filed.



git commit file name

![image-20210308134856545](C:\Users\julie.yang\AppData\Roaming\Typora\typora-user-images\image-20210308134856545.png)

press Esc + : wq

this is vim environment. add the comment here

git commit -m "comment" file name

![image-20210308142220199](C:\Users\julie.yang\AppData\Roaming\Typora\typora-user-images\image-20210308142220199.png)

show the log more pretty

![image-20210308143055694](C:\Users\julie.yang\AppData\Roaming\Typora\typora-user-images\image-20210308143055694.png)

![image-20210308143205356](C:\Users\julie.yang\AppData\Roaming\Typora\typora-user-images\image-20210308143205356.png)

git log --oneline

git reflog

基于索引进行版本切换

 git reset --hard 50877a1 工作区和缓存区都刷新

### 分支操作

![image-20210309113655026](C:\Users\julie.yang\AppData\Roaming\Typora\typora-user-images\image-20210309113655026.png)



衝突的解決

![image-20210309115246841](C:\Users\julie.yang\AppData\Roaming\Typora\typora-user-images\image-20210309115246841.png)



提交对象链条

![image-20210309121932969](C:\Users\julie.yang\AppData\Roaming\Typora\typora-user-images\image-20210309121932969.png)



### Eclipse Git

1 將現有工程加入git

![image-20210309155153660](C:\Users\julie.yang\AppData\Roaming\Typora\typora-user-images\image-20210309155153660.png)

忽略Eclipse的管理文件。

![image-20210309153602553](C:\Users\julie.yang\AppData\Roaming\Typora\typora-user-images\image-20210309153602553.png)

2  導入工程

![image-20210309154923185](C:\Users\julie.yang\AppData\Roaming\Typora\typora-user-images\image-20210309154923185.png)

![image-20210309155352008](C:\Users\julie.yang\AppData\Roaming\Typora\typora-user-images\image-20210309155352008.png)





![image-20210309155612709](C:\Users\julie.yang\AppData\Roaming\Typora\typora-user-images\image-20210309155612709.png)



转换成maven工程

![image-20210309155738523](C:\Users\julie.yang\AppData\Roaming\Typora\typora-user-images\image-20210309155738523.png)



### 解决conflict

先pull工程

在文件中显示冲突的地方

````
package ca.xenex.ProTools.abatorDTO;

import ca.xenex.ProTools.abatorDTO.BaseDTO;

/**
 * 
 * The Class Aaa.   
 */
public class Aaa extends BaseDTO { 

	private static final long serialVersionUID = 1L;
	
	String s = "change file";
<<<<<<< HEAD
	String s = "test conflict test2";
=======
	String s = "test conflict test1";
>>>>>>> branch 'master' of http://192.168.2.5/xenex/learningit.git
}

````

点击冲突文件的右键，选择team->merge tool 会显示冲突改变的地方

![image-20210309162106106](C:\Users\julie.yang\AppData\Roaming\Typora\typora-user-images\image-20210309162106106.png)



\##Build Folder in SuretyMaster
WARNING: Git requires there to be at least one file for a folder to be TRACKED.
1) Create a dummy file in the build folder for it to be committed. THIS FILE IS EMPTY
build/.gitignore2) Need to modify .gitignore to ignore all the contents of the build folder
/build/*3) FORCE ADD the file in the folder so the folder is added
git add -f build/.gitignore
git add .gitignore(I did the rest of the commit and push in eclipse.)



pwd look at the folder

## Vim has two modes.

### 1. Insert mode (Where you can just type like normal text editor. Press i for insert mode)

### 2. Command mode (Where you give commands to the editor to get things done . Press ESC for command mode)

vim ABC，创建一个文件名为ABC 的文件

Most of them below are in command mode

- x - to delete the unwanted character
- u - to undo the last the command and U to undo the whole line
- CTRL-R to redo
- A - to append text at the end
- :wq - to save and exit
- :q! - to trash all changes
- dw - move the cursor to the beginning of the word to delete that word
- 2w - to move the cursor two words forward.
- 3e - to move the cursor to the end of the third word forward.
- 0 (zero) to move to the start of the line.
- d2w - which deletes 2 words .. number can be changed for deleting the number of consecutive words like d3w
- dd to delete the line and 2dd to delete to line .number can be changed for deleting the number of consecutive words

The format for a change command is: operator [number] motion
-operator - is what to do, such as d for delete
\- [number] - is an optional count to repeat the motion
\- motion - moves over the text to operate on, such as w (word),
$ (to the end of line), etc.

- p - puts the previously deleted text after the cursor(Type dd to delete the line and store it in a Vim register. and p to put the line)
- r - to replace the letter e.g press re to replace the letter with e
- ce - to change until the end of a word (place the cursor on the u in lubw it will delete ubw )
- ce - deletes the word and places you in Insert mode
- G - to move you to the bottom of the file.
- gg - to move you to the start of the file.
  Type the number of the line you were on and then G
- % to find a matching ),], or }
- :s/old/new/g to substitute 'new' for 'old' where g is globally
- / backward search n to find the next occurrence and N to search in opposite direction
- ? forward search
- :! to run the shell commands like :!dir, :!ls
- :w - TEST (where TEST is the filename you chose.) . Save the file
- v - starts visual mode for selecting the lines and you can perform operation on that like d delete
- :r - Filename will insert the content into the current file
- R - to replace more than one character
- y - operator to copy text using v visual mode and p to paste it
- yw - (copy)yanks one word
- o - opens a line below the cursor and start Insert mode.
- O - opens a line above the cursor.
- a - inserts text after the cursor.
- A - inserts text after the end of the line.
- e - command moves to the end of a word.
- y - operator yanks (copies) text, p puts (pastes) it.
- R - enters Replace mode until <ESC> is pressed.
- ctrl-w to jump from one window to another

type a command :e and press ctrl+D to list all the command name starts with :e and press tab to complete the command





# 将本地文件夹转换为一个 GIT 项目的一般步骤和untracked content问题

> 将本地文件夹init成git库并且上传到新建的github仓库

### 一、`打开命令行终端，进入项目所在的本地目录，将目录初始化为一个 Git 项目`

```
$ git init
复制代码
```

此时会生成一个隐藏的.git文件，表示创建了一个空的git仓库

![img](https://user-gold-cdn.xitu.io/2019/5/28/16afd18ace39d4a5?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

### 二、 `将所有文件放进新的本地 git 仓库`

```
$ git add .
复制代码
```

> 如果你本地已经有 .gitignore 文件，会按照已有规则过滤不需要添加的文件。如果没有，可以根据手动创建.gitignore文件跟git仓库进行关联

### 三、 `将添加的文件提交到仓库`

```
$ git commit -m "xxx"
复制代码
```

### 四、 `登陆你的github账户，创建新的仓库`

![img](https://user-gold-cdn.xitu.io/2019/5/28/16afd2a7fb7d45fb?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

为了避免冲突，先不要勾选 README 和 LICENSE 选项

![img](https://user-gold-cdn.xitu.io/2019/5/28/16afd2bd13bfb015?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

### 六、 `在生成的项目主页上，复制仓库地址`

类似于 [github.com/wozaixuehtm…](https://github.com/wozaixuehtml5/dom-diff.git)

> https跟ssh的都可以

![img](https://user-gold-cdn.xitu.io/2019/5/28/16afd2e7bd6a09ee?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

### 七、 `回到命令行终端界面，将本地仓库关联到远程仓库`

```
$ git remote add origin https://github.com/wozaixuehtml5/dom-diff.git
复制代码
```

可运行以下命令查看结果：

```
$ git remote -v
复制代码
```

### 八、 `提交代码到 GitHub 仓库`

```
$ git push origin master
复制代码
```

### END：解决'modified content, untracked content'的提示报错

到这里基本已经可以了，但是当所选本地文件夹下还有别的文件夹的时候，会出现git仓库跟踪不到这些文件夹的情况，提示报错 'modified content, untracked content'，大体是这样的：

![img](https://user-gold-cdn.xitu.io/2019/5/27/16af88308b966128?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

这里提示的dom-diff就是我的init仓库中的一个文件夹

此时只需要

```
git rm -rf --cached dom-diff  
git add dom-diff //重新跟踪dom-diff文件
复制代码
```

![img](https://user-gold-cdn.xitu.io/2019/5/28/16afd3aa3cf6a876?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

然后就大功告成，可以安安心心的code了！

参考资料：[blog.csdn.net/solo_ws/art…](https://blog.csdn.net/solo_ws/article/details/77095901) [stackoverflow.com/questions/5…](https://stackoverflow.com/questions/50167969/how-to-fix-modified-content-untracked-content-in-git/52722775)







````
git-graph.view: Git Graph: View Git Graph
git-graph.addGitRepository: Git Graph: Add Git Repository... (used to add sub-repos to Git Graph)
git-graph.clearAvatarCache: Git Graph: Clear Avatar Cache
git-graph.endAllWorkspaceCodeReviews: Git Graph: End All Code Reviews in Workspace
git-graph.endSpecificWorkspaceCodeReview: Git Graph: End a specific Code Review in Workspace... (used to end a specific Code Review without having to first open it in the Git Graph View)
git-graph.fetch: Git Graph: Fetch from Remote(s) (used to open the Git Graph View and immediately run "Fetch from Remote(s)")
git-graph.removeGitRepository: Git Graph: Remove Git Repository... (used to remove repositories from Git Graph)
git-graph.resumeWorkspaceCodeReview: Git Graph: Resume a specific Code Review in Workspace... (used to open the Git Graph View to a Code Review that is already in progress)
git-graph.version: Git Graph: Get Version Information
````





# Git实用教程（九）| 使用VS Code进行Git可视化操作

https://cloud.tencent.com/developer/article/1662720

2020-07-15阅读 2K0

**Git实用教程专栏回顾**

- Git实用教程（一） | 为什么需要版本控制？（以嵌入式项目开发为例）
- Git实用教程（二） | Git简介及安装详解
- Git实用教程（三） | Git本地库操作（仓库初始化、提交修改）
- Git实用教程（四） | Git本地库操作（查看提交历史、版本前进回退）
- Git实用教程（五） | Git本地库操作（分支管理）
- Git实用教程（六）| Github远程库操作（创建、拉取、推送、克隆）
- Git实用教程（七）| Github远程库操作（邀请远程库协作者）
- Git实用教程（八）| Github远程库操作（使用PR贡献代码）

### **0. 使用VS Code进行Git可视化操作** 

VS Code内置了对Git的支持，可以使用图形化界面方便的进行版本控制，比如暂存，提交更新，推送，拉取这些常规操作，特别在比较文件差异方面有着得天独厚的优势，但是前提是：

- **已经安装了VScode并且了解其使用**
- **电脑上必须安装了Git并且你了解Git的常用命令行操作**



### **1.设置git.path** 

Git的安装可以参考我的第二篇文章（Git实用教程（二） | Git简介及安装详解），在安装过程中有一步如下：

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/9sre1cb8j6.png?imageView2/2/w/1620)

大多数人经常使用的是Git自带的命令行工具Git bash，为了不影响其它windows命令，**安装时会选择第一个**，那么，VS Code启动后是找不到Git所在位置的，必须要自己设置`git.path`，否则打开**Git存储库**(如果是普通文件夹，请先使用`git init`初始化为Git仓库)会出现如下提示：

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/ktn9zzdjia.png?imageView2/2/w/1620)

切换到源代码管理视图，也可以看到错误提示：

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/o42rc872g1.png?imageView2/2/w/1620)

解决方法如下：

1. 使用`Ctrl+Shift+P`打开命令面板，输入`setting`，选择`首选项：打开设置(json)`：

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/oome3iz7sv.png?imageView2/2/w/1620)

1. 添加`git.path`一项，目录为当前电脑上`git.exe`所在目录，如图：

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/lcatbfli2v.png?imageView2/2/w/1620)

1. 保存，重新用VS Code打开Git存储库：

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/leyfbmi23u.png?imageView2/2/w/1620)

### **2.本地库基本操作** 

## **2.1.修改文件**

对本地库进行修改，创建一个新的`test.c`文件，并添加一段代码，可以看到**VS Code会自动显示出更改**：

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/59hom62a30.png?imageView2/2/w/1620)

## **2.2.暂存修改**

在命令行中将当前修改暂存使用命令`git add <文件名>`，但在VS Code中，可以以图形化方式操作，并且可以清楚到当前暂存区和修改区的内容：

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/5b7b17jih3.png?imageView2/2/w/1620)

## **2.3.提交更新到本地库**

同样，在命令行中将暂存区内容提交到本地库使用命令`git commit -m  <提交信息>`，在VS Code中，可以以图形化方式操作：

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/ambzygomlm.png?imageView2/2/w/1620)

点击提交按钮之后，会弹出输入提交信息的对话框，输入提交信息即可：

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/4cow6osquk.png?imageView2/2/w/1620)

修改区和暂存区内容清空，如图：

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/e3pz1zy3xi.png?imageView2/2/w/1620)

### **3.安装Git扩展添加更多功能** 

VS Code内部集成的Git仅仅支持一些基本操作，我们可以安装Git扩展来支持更多功能，比如`git-extension-pack`这个扩展：

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/s7n74s440w.png?imageView2/2/w/1620)

在VS Code扩展搜索`git`，选择该扩展，安装：

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/7061k8hj1p.png?imageView2/2/w/1620)

## **3.1.查看提交历史**

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/zx5nm11nbo.png?imageView2/2/w/1620)

## **3.2.随时查看当前内容的状态**

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/4qnvt4mij1.png?imageView2/2/w/1620)

## **3.3.随时查看仓库状态**

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/qolavld15q.png?imageView2/2/w/1620)

## **3.4.查看文件差异**

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/b8ezdlt9az.png?imageView2/2/w/1620)

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/z1muwmvzqv.jpeg?imageView2/2/w/1620)

## **3.5. 切换当前分支**

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/911haw0jyo.jpeg?imageView2/2/w/1620)

## **3.6. 查看分支之间的差异和合并分支**

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/sxv83pbg7a.jpeg?imageView2/2/w/1620)

可以看到结果如下：

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/nzser9rlrh.jpeg?imageView2/2/w/1620)

查看文件差异之后将分支合并到master，打开命令行输入`git merge`：

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/t116div360.png?imageView2/2/w/1620)

选择要合并到当前分支的分支：

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/y2bcaq1akd.png?imageView2/2/w/1620)

如果可以自动合并失败，则会出现以下界面：

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/r607ue84c6.jpeg?imageView2/2/w/1620)

直接在编辑器中手动修改冲突，修改之后保存即可：

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/9xxzh8vxuc.png?imageView2/2/w/1620)

### **4.远程库操作** 

注：如果本地的存储库是克隆下来的，或者已经手动添加了远程库地址，则可以直接进行推送和拉取操作。

## **4.1.在Github上新建一个远程库**

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/tubojm462m.png?imageView2/2/w/1620)

## **4.2.添加远程库**

使用`Ctrl+Shift+P`打开命令面板，输入`git add`，选择`添加远程库`：

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/4m5mffk3ae.png?imageView2/2/w/1620)

填写**远程库地址的别名**：

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/swk6465fn1.png?imageView2/2/w/1620)

填写远程库地址：

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/kh1tdtr9d3.png?imageView2/2/w/1620)

## **4.3.推送内容到远程库**

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/wjcy2ajo40.png?imageView2/2/w/1620)

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/14qjh59i5f.png?imageView2/2/w/1620)

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/zlxq28qqay.png?imageView2/2/w/1620)

### **5.更多Git操作** 

至此，使用VS Code配合Git进行一些本地库和远程库基本操作就讲述完了，些许有些意犹未尽，其实，**VS Code内置的Git扩展还有很多命令可以执行**，比如：初始化本地库，克隆远程库，拉取远程库内容等等，使用`Ctrl+Shift+P`打开命令面板，输入`git`即可看到所有，有兴趣的小伙伴可以自己尝试一下：

![img](https://ask.qcloudimg.com/http-save/yehe-1088047/6bkvgy0kd2.png?imageView2/2/w/1620)

本文分享自微信公众号 - Mculover666（Mculover666），作者：mculover666

原文出处及转载信息见文内详细说明，如有侵权，请联系 yunjia_community@tencent.com 删除。

原始发表时间：2019-10-26

本文参与[腾讯云自媒体分享计划](https://cloud.tencent.com/developer/support-plan)，欢迎正在阅读的你也加入，一起分享。

[举报](javascript:;)

点赞 2

