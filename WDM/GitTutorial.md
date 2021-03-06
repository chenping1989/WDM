##更改Git Bash的默认路径
在打开git bash时，每次都是在C:\Uer路径下，每次都需要先用cd命令转换到自己需要工作的路径（cd  /f/dss）。
修改打开git bash 时的默认的路径就可以不用每次都使用cd命令转换到需要管理的目录。

修改默认路径方法：右击Git Bash图标--->选择Short Cut标题栏--->修改Start in为想要管理的目录。



##创建版本库，添加文件
初始化一个Git仓库，使用git init命令。
添加文件到Git仓库，分两步：
	第一步，使用命令git add <file>，注意，可反复多次使用，添加多个文件；
	第二步，使用命令git commit -m "The presentation of the submission..."，完成。

##查看文件状态
git status命令可以列出当前目录所有还没有被git管理（尚未git add）的文件和被git管理(已经git add)且被修改但还未提交(git commit)的文件。
git diff filename 顾名思义就是查看difference，显示修改内容
##版本回退
HEAD指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭：
	使用命令
	$git reset --hard commit_id。
穿梭前，用git log可以查看提交历史，以便确定要回退到哪个版本。
要重返未来，用git reflog查看命令历史，以便确定要回到未来的哪个版本。

##工作区和暂存区
我们把文件往Git版本库里添加的时候，是分两步执行的：
	第一步是用git add把文件添加进去，实际上就是把文件修改添加到暂存区；
	第二步是用git commit提交更改，实际上就是把暂存区的所有内容提交到当前分支。

##撤销修改：
场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，
		用命令git checkout -- filename.xx。

场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，
		第一步用命令git reset HEAD filename.xx，就回到了场景1，
		第二步按场景1操作。

场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库。

##删除文件
1. 从版本库中删除该文件：先git rm filename 然后 git commit -m "word..."
	$ git rm test.txt
	rm 'test.txt'
	$ git commit -m "remove test.txt"
	[master d17efd8] remove test.txt
	 1 file changed, 1 deletion(-)
	 delete mode 100644 test.txt
2. 删错了，因为版本库里还有呢，所以可以很轻松地把误删的文件恢复到最新版本：
	$ git checkout -- test.txt

##远程仓库
1. 要关联一个远程库，使用命令:
	git remote add origin https://github.com/NemoLan/WDM.git
	或者：
	git remote add origin git@github.com:NemoLan/WDM.git
	（TIPS:	Git支持多种协议，默认的git://使用ssh，但也可以使用https等其他协议。使用https除了速度慢以外，还有个最大的麻烦是每次推送都必须输入口令，但是在某些只开放http端口的公司内部就无法使用ssh协议而只能用https。
	不必担心地址泄露，因为即使他人关联了我的远程库，也会由于没有SSH Key公钥而无法推送的。
	）

2. 关联后，第一次推送master分支的所有内容,使用命令:
	git push -u origin master；
	此后，每次本地提交（git commit）后，只要推送最新修改，就可以使用命令：
	<r>git push origin master</r>
	
3. 从远程仓库克隆到本地
	a.用命令git clone克隆一个本地库：
		git clone git@github.com:NemoLan/WDM.git
	b.要克隆一个仓库
		首先，必须知道仓库的地址，然后使用git clone命令克隆。
		Git支持多种协议，包括https，但通过ssh支持的原生git协议速度最快。 

##创建与合并分支
1. Git用HEAD指向当前分支master（或者其他），master指向最新的提交，再用HEAD指向master，就能确定当前分支，以及当前分支的提交点。
2. 当我们创建新的分支，例如dev时，Git新建了一个指针叫dev，指向master相同的提交，再把HEAD切换到dev，就表示当前分支在dev上。（Git创建一个分支很快，因为除了增加一个dev指针，改改HEAD的指向，工作区的文件都没有任何变化！）
	创建新的分支dev：
	$ git branch dev
	HEAD切换到dev分支：
	$ git checkout dev
然后用git branch命令查看当前分支，列出的所有分支中，当前分支前面会标一个*号。
从现在开始，对工作区的修改和提交就是针对dev分支了（git add 和git commit都在dev分支上执行，而master保持不变），比如新提交一次后，dev指针往前移动一步，而master指针不变。
3. 在dev上的工作完成了，就可以把dev合并到master上，方法就是直接把master指向dev的当前提交。
	步骤1.切换回master分支：
	$ git checkout master
	步骤2.把dev分支的内容合并到master分支上：
	$ git merge --no-ff -m "merge with no-ff" dev
%%git merge 命令用于合并指定分支（此处指定dev）到当前分支（即master）。其中--no-ff表示禁用Fast Forward模式，而用普通模式合并，合并后的历史有分支，能看出来曾经做过合并
（合并完分支后，甚至可以删除dev分支。删除dev分支就是把dev指针给删掉。
	$ git branch -d dev）
	
4. 当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。
	用git log --graph命令可以看到分支图。
Git鼓励大量使用分支：

查看分支：git branch

创建分支：git branch <name>

切换分支：git checkout <name>  %Switched to branch '<name> '

创建+切换分支：git checkout -b <name> %Switched to a new branch '<name>'

合并某分支到当前分支：git merge <name>

删除分支：git branch -d <name>

分支图：git log --graph

##BUG分支
修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；
当手头工作没有完成时，先把工作现场git stash一下，然后去修复bug，修复后，$ git stash list再git stash pop，回到工作现场。
步骤如下：
把当前工作现场“储藏”起来：
$ git stash 
假定需要在master分支上修复，先切换到master：
$ git checkout master 
从master创建临时分支issue-101：
$ git checkout -b issue-101 

...在分支issue-101上修复BUG...
修复完毕再提交：
$ git add readme.txt 
$ git commit -m "fix bug 101"
修复完成后，切换到master分支：
$ git checkout master  
把分支issue-101的内容合并到master分支上：
$ git merge --no-ff -m "merged bug fix 101" issue-101  
删除issue-101分支：
$ git branch -d issue-101   
干完临时的BUG修复工作，还是得回复到主线上：
看看工作现场藏哪了：
$ git stash list
恢复现场：
方法1：$git stash pop
方法2：	$ git stash apply stash@{0}
		$ git stash drop stash@{0}


Q&A
Q：虽然分支issue-101修复bug后并入了master，但是dev分支并没有修复这个bug。当dev分支开发完，要并入master，不是会有冲突了吗？ 
A:只要不是同时修改到同一个文件就不会有冲突的，冲突产生的原因是在两个分支上同时修改了某个文件的同一位置才会出现。