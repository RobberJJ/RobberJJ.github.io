---
title: Github超过100M的大文件上传
date: 2018-05-02 14:13:50
tags:
    - github
    - 大文件
    - upload
    - 上传
categories: "随笔"
---

通常我们commit完成之后，往Github上push的时候，单个文件大小不会超过100M，所以基本不会遇到这种情况。
但是，总会遇到的，当单个文件超过100M的时候，push的时候会出现下面这样的Error：

>remote: Resolving deltas: 100% (472/472), done.
remote: error: GH001: Large files detected. 
You may want to try Git Large File Storage - https://git-lfs.github.com.

<!--  more -->

或者：

>Total 3007 (delta 664), reused 0 (delta 0)
remote: error: GH001: Large files detected.
remote: error: Trace: 7b7de6b9372ee392e0f3961b05ea6f33
remote: error: See http://git.io/iEPt8g for more information.
remote: error: File  XXX/XXX/XXX is 234 MB; this exceeds GitHub‘s file size limit of 100.00 MB
remote: error: File  XXX/XXX/XXX is 234 MB; this exceeds GitHub‘s file size limit of 100.00 MB。

<!-- more --> 

上面错误的原因很好理解就是GitHub不允许直接上传大文件（超过100M）的文件到远程仓库，若要想继续提交可以尝试使用大文件支持库：[https://git-lfs.github.com](https://link.jianshu.com/?t=https%3A%2F%2Fgit-lfs.github.com)
LFS使用的简单步骤：

*   安装git - lfs到本机
这里没有什么难度，根据官网安装就好。

之后就是提交大文件的顺序，下面是官网的顺序：

1. 安装Git命令行扩展。只需要设置一次Git LFS。
在项目目录下，执行以下命令：
>git lfs install

2. 选择您希望Git LFS管理的文件类型（或直接编辑.gitattributes）。您可以随时配置其他文件扩展名。这一步成功后会生成一个gitattributes文件
>git lfs track “* .a” --这里的 “ *.a "就是你要上传的大文件的路径

3. 添加并commit gitattributes文件
>git add .gitattributes

4. 然后再添加大文件到本地缓存区
>git add demo.a
git commit -m "提交.a大文件"
git push

以上，按照上述的方法，第一次上传大文件基本还是会报错，而且百思不得其解~
尼玛，新手入坑总是这个尿性。。

如下图：
![2200042-a1a69b95caa64404.png](https://upload-images.jianshu.io/upload_images/6080248-b5b22f1f9d1a1f7f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


出现上述问题，先reset 到你刚才commit的前一个commit,(意思就是撤销刚才的commit，回到起点再来一次。)

第一、二步同上，第三步的时候，先将 .gitattributes文件，单独commit，然后push，完成之后，在进行第四部(第四部add 之后 执行`git lfs ls-files`应该能看见东西了)。
到这里应该就没问题了。
