[TOC]


## 第一次上传git：

1. 创建本地仓库

先进入要上传的文件夹，然后右键选择Git Bash，例如第一次上传本地的 G:\mdNote 文件夹

<img src="images/git上传本地仓库/image-20200903215041329.png" alt="image-20200903215041329" style="zoom:50%;" />

> step1	执行初始化
>
> ​		`git init`
>
> step2	将文件添加到本地仓库
>
> ​		`git add .`				//添加当前文件夹下的所有文件
>
> ​		`git add **.cpp`		//添加当前文件夹下的**.cpp这个文件	
>
> step3	输入提交说明
>
> ​		`git commit -m "说明内容"`          //引号中的内容为对该文件的描述
>
> step4	本地仓库关联github远程仓库
>
> ​		`git remote add origin git@github.com:panchengrui/NoteBook.git`
>
> step5	提交文件
>
> ​		`git push origin master`





## 更新一个已经存在的本地仓库（上传本地仓库到远程仓库）

`git add .`

`git commit -m "说明内容"`

`git push origin master`

