# Git

### 基本命令

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