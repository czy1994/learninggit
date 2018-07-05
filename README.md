# learninggit
#全局配置我的基础信息
注意git config命令的--global参数，用了这个参数，表示你这台机器上所有的Git仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和Email地址。
	git config --global user.name "Your Name"
	git config --global user.email "email@example.com"
	
	git config --global core.autocrlf false 解决windows和linux的换行符问题，禁止自动转换
#创建版本库
	git init  --变成Git可以管理的仓库
	
#版本管理操作
	git add 将文件加入git管理
		-f 强制添加到git，因为如果文件被.gitignore忽略
	git commit -m "wrote a readme file" 提交到本地仓库
		-m后面输入的是本次提交的说明
	git status testGit.txt  查看仓库文件版本状态
	git diff testGit.txt 比较内容变化
	git log --pretty=oneline testGit.txt 查看提交日志
		--pretty=oneline  简化显示日志记录
		git log --graph --pretty=oneline --abbrev-commit
		*--graph 可以看到分支合并图*
	*git log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit*
	git reset --hard HEAD^ 回退上一版本
		*--hard 稍后记得注释*
	*git reflog 查看每一次命令*
	git checkout -- testGit.txt 
		文件在工作区的修改全部撤销，这里有三种情况:
		*修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
		*已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。
		*已经添加到暂存区，但是没有修改，这时，git checkout 则无法撤销，可以用git reset HEAD testGit.txt 撤销，然后再git checkout。
		
		*git checkout -- file命令中的--很重要，没有--，就变成了“切换到另一个分支”的命令*
	git rm testGit.txt 删除文件
		执行该命令后，变相是：文件系统删除文件，然后执行了git add
		*若此时想恢复，则执行git reset HEAD filename,然后git checkout -- filename*

#git命令 注意点	
	*若新增文件，但是没有执行git add加入git的暂存区的话，在父文件夹直接git commit的话，将会失败（尽管你其他文件已经修改了），必须全部加入git暂存区后，才能一次性commit成功。
	*暂存区使用注意：
		第一次修改 -> git add -> 第二次修改 -> git commit
		当用git add命令后，在工作区的第一次修改被放入暂存区，准备提交，但是，在工作区的第二次修改并没有放入暂存区，所以，git commit只负责把暂存区的修改提交了，也就是第一次的修改被提交了，第二次的修改不会被提交。
	
		git add 这个命令需要注意含义！！！！
		
		如果文件修改后，没有执行git add，而是直接git commit的话，则会默认先ADD 再COMMIT（前提是没有add过）。反之，如果已经add过了未commit，此时修改文件后再commit的话，是不会commit第二次修改的内容的。

#使用github远程仓库
1.在GitHub上，可以任意Fork开源仓库；
2.自己拥有Fork后的仓库的读写权限；
3.可以推送pull request给官方仓库来贡献代码。
##一、将本地仓库关联到github仓库
	1. 本地电脑使用ssh-keygen生成公钥、密钥，将公钥放入github个人设置中的ssh配置；然后在github上新建仓库
	2.本地项目与github上的仓库关联
		git remote add origin https://github.com/czy1994/learngit.git
		添加后，远程库的名字就是origin，这是Git默认的叫法，也可以改成别的
	3.将本地项目推送到远程库上
		git push -u origin master
		
		把本地库的内容推送到远程，用git push命令，实际上是把当前分支master推送到远程。
		由于远程库是空的，我们第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。
	4.把本地master分支的最新修改推送至GitHub
		git push origin master
##二、将github已有仓库clone到本地关联
	git clone git@github.com:czy1994/learninggit.git
	或者
	git clone https://github.com/czy1994/learninggit.git
	实际上，Git支持多种协议，默认的git://使用ssh，但也可以使用https等其他协议。
	使用https除了速度慢以外，还有个最大的麻烦是每次推送都必须输入口令，但是在某些只开放http端口的公司内部就无法使用ssh协议而只能用https。
	Git支持多种协议，包括https，但通过ssh支持的原生git协议速度最快。

#git 分支管理
	参考：*https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/001375840038939c291467cc7c747b1810aab2fb8863508000*
	
	*HEAD 指向分支；master或者其他分支 指向提交
	例如：   当我们创建新的分支，例如dev时，Git新建了一个指针叫dev，指向master相同的提交，再把HEAD指向dev，就表示当前分支在dev上。从现在开始，对工作区的修改和提交就是针对dev分支了，比如新提交一次后，dev指针往前移动一步，而master指针不变。
	假如我们在dev上的工作完成了，就可以把dev合并到master上。Git怎么合并呢？最简单的方法，就是直接把master指向dev的当前提交，就完成了合并
##分支管理相关命令
	git checkout -b dev “-b参数：创建dev分支并切换到dev分支”
		相当于：
			git branch dev 创建
			git checkout dev 切换分支
	git branch 列出所有分支，当前分支前面会标一个*号
	git merge dev 合并dev分支到当前分支
	git branch -d dev 删除dev分支
	*git stash* 存储当前工作现场，等以后恢复现场后继续工作（没有被Git管理的文件不会存储，需git add之后才会存储）
	*git stash list*查看存储的工作现场
	*git stash apply stash@{0}* 恢复指定存储，恢复后不会删除stash列表数据
	*git stash drop stash@{0}*删除stash数据
	*git stash pop*恢复指定存储，恢复后删除pop的stash数据
##代码合并冲突问题
	当冲突后，会进入冲突解决模式MERGING，git status可查看冲突文件。
	*Git用<<<<<<<，=======，>>>>>>>标记出不同分支的内容
		例如：
			<<<<<<< HEAD
			 master
			=======
			 f1
			>>>>>>> f1
		代表，当前分支的修改为HEAD下面的内容，既master，其他分支f1的修改内容为f1。
	*本地解决完冲突之后，需要使用以下命令声明冲突解决：
		git add <filename>
		git commit -m "comment"
		*注意：*commit后面没有加文件名，因为这样才是标志为冲突解决，如果加了文件名则是提交，git会报错（fatal: cannot do a partial commit during a merge.）
##分支管理策略
	git merge --no-ff -m "merge with no-ff" dev
		通常，合并分支时，如果可能，Git会用Fast forward模式，但这种模式下，删除分支后，会丢掉分支信息。
		如果要强制禁用Fast forward模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。	*合并分支时，加上--no-ff参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而fast forward合并就看不出来曾经做过合并。*
###分支策略
	master 主分支
	dev 开发分支（开发分支下面又可以有各自开发人员的分支）
	bug bug分支
		bug分支参考：*https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00137602359178794d966923e5c4134bc8bf98dfb03aea3000*
	feature 新功能分支 开发一个新feature，最好新建一个分支
		如果要丢弃一个没有被合并过的分支，可以通过git branch -D <name>强行删除
#多人协作
	git remote -v 查看远程库的信息
		origin  git@github.com:czy1994/learninggit.git (fetch)
		origin  git@github.com:czy1994/learninggit.git (push)
		fetch 抓取   push 推送 （若无推送权限，则不会有push）
	git push origin <分支名> 推送分支
	git checkout -b dev origin/dev	创建远程origin的dev分支到本地
	git branch --set-upstream-to=origin/dev dev 指定本地dev分支与远程origin/dev分支的链接
	git pull 抓取远程服务器的最新提交记录（可能会有冲突）
##Rebase操作
	https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0015266568413773c73cdc8b4ab4f9aa9be10ef3078be3f000

	git rebase 可以把本地未push的分叉提交历史整理成直线
		rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比。
#标签管理
	git tag [name] [commit id] 打一个新标签(默认最新提交的commit，也可以指定commit id)
		-a 指定标签名
		-m 指定说明文字
		-d 删除标签
		*git tag -a v0.1 -m "version 0.1 released" 1094adb
		*git tag -d v0.1
	git tag 查看所有标签
		注意，标签不是按时间顺序列出，而是按字母排序的
	git show <tagname> 查看标签信息
	git push origin <tagname> 推送标签到远程
	git push origin --tags 一次性推送全部尚未推送到远程的本地标签
	
	*如果标签已经推送到远程，要删除远程标签*
		1.先本地删除 git tag -d v0.9
		2.再从远程删除 git push origin :refs/tags/v0.9
#自定义Git
##忽略特殊文件
	GitHub已经为我们准备了各种配置文件	 https://github.com/github/gitignore
	忽略文件的原则是：
		*忽略操作系统自动生成的文件，比如缩略图等；
		*忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如Java编译产生的.class文件；
		*忽略你自己的带有敏感信息的配置文件，比如存放口令的配置文件。
	git check-ignore -v <filename> 检查该文件在.gitignore中哪里被忽略
##配置别名（重要）
	https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/001375234012342f90be1fc4d81446c967bbdc19e7c03d3000
	
	例子：
		*git config --global alias.st status
		*git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
##搭建Git服务器
	https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00137583770360579bc4b458f044ce7afed3df579123eca000#0
#注意
1.文件在哪一个分支被commit，就是哪个分支的提交，git add不算。
2.标签总是和某个commit挂钩。如果这个commit既出现在master分支，又出现在dev分支，那么在这两个分支上都可以看到这个标签
3.Windows .gitignore文件会提示必须输入文件名，在文本编辑器里“保存”或者“另存为”就可以把文件保存为.gitignore了

#使用码云
	https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00150154460073692d151e784de4d718c67ce836f72c7c4000
		
		


	
	

