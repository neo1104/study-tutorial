# Git 使用手册

##1.Git 操作流程

![](http://d.pr/i/169s8+)

##2.Git概念

##3.Git常用命令介绍
###3.1Git基础命令
#### git init

要使用Git进行版本管理，必须先初始化仓库。Git是使用**git init**命令进行初始化的。
初始化git仓库。该命令会在当前目录下生成.git目录。.git目录中存储着管理当前目录内容所需的仓库数据.

我们将当前目录称为**附属于该仓库的目录树**

#### git status
git status命令用于查看Git仓库的状态，这是一个十分常用的命令，请务必牢记。

工作树和仓库在被操作的过程中，状态会不停的发生变化。在Git操作过程中经常使用**git status**开查看当前的状态，可谓基本中的基本。下面是git status命令的基础输出:

		On branch master
		Initial commit
		nothing to commit (create/copy files and use "git add" to track)
		
	
结果显示了我们正处于master分支下。同时也显示目前没有可提交的内容。这里我们创建一个README.md文件作为管理对象，为第一次文件提交做准备。
	
		touch README.md
		git status
		On branch master
		Initial commit
		Untracked files:
			(use "git add <file>..." to include in what will be committed)
		README.md
	nothing added to commit but untracked files present (use "git add" to track)	
	

我们可以看到在Untacked files中显示了README.md文件。类似的，只要对Git的工作书或者仓库进行操作，git status命令的结果就会相应的发生变化。

#### git add
git add 命令用于将文件(管理对象)加入暂存区(Stage或者Index)。暂存区是提交之前的一个临时区域。

像上面所述的那样，如果只是在Git所管理的目录中创建了一个文件，那么该文件并不会被记入Gi仓库的版本管理对象中。因此我们使用git status命令查看时，README.md文件会被显示在Untacked files中。

要想让README.md文件成为Git仓库的管理对象，就需要用git add命令:

		git add README.md
		git status
		On branch master
		Initial commit
		Changes to be committed:
			(use "git rm --cached <file>..." to unstage)
		new file:   README.md
		
将README.md文件加入暂存区后，git status的输出结果发生了变化，README.md显示在Changes to be committed了。	
#### git commit
git commit命令可以将当前暂存区中的文件实际保存到仓库的历史记录中。通过这些历史记录，我们可以在工作树中复原文件。

我们来实际执行以下git commit命令。

	git commit -m "First commit"
	[master (root-commit) 877e120] First commit
	1 file changed, 0 insertions(+), 0 deletions(-)
	create mode 100644 README.md
	
命令中-m选项后的"First commit"称作提交信息，是对这个提交的概述。

刚才我们只是简洁的记述了一行提交信息，如果想要记述的更为详细，需要去掉-m选项。直接执行**git commit**命令，编辑器(vim)就会被启#动,并显示如下结果:

	#Please enter the commit message for your changes.Lines starting
	#with '#' will be ignord, and an empty message abort the commit.
	#On branch master
	#
	#Initial commit
	#Changes to be committed:
	#		(use "git rm --cached <file>..." to unstage)
	#	new file:   README.md
	

在编辑中提现信息的格式如下:

* 第一行: 用一行文字简述提交的更改内容
* 第二行: 空行
* 第三行: 记录更改的原因和详细内容。

只要按照上面的格式进行输入，今后便可以通过确认气质的工具或命令看到这些记录。

执行完git commit命令后，我们再来查看当前的状态:

	git status
	On branch master
	nothing to commit, working directory clean
	
当前工作树出于刚刚完成提交的状态，所以结果显示没有更改。

#### git log
git log命令可以查看以往仓库中提交的日志。我们来查看下刚才的git commit命令是否被记录:

	git log
	commit 877e12095295286259be9dc69e2eb36fcec54318
	Author: fang feng <fengf1104@gmail.com>
	Date:   Fri Jun 17 13:24:23 2016 +0800

    First commit
    
上面显示了我们刚刚提交的日志。

commit旁边的"877e12"是执行这次提交HASH值。Author栏显示了本次提交的用户名、邮箱。
Date显示了提交的日期。最后就是本次的提交信息。

如果只是想显示第一行提交信息，可以执行**git log --pretty=short**命令。

如果只想显示指定目录的提交日志，可以在git log后面加上目录名，如果加的是文件名，只会显示与该文件相关的日志，git log README.md。

如果想要查看本次提交所带来的改动，可以在git log后面加上-p选项，文件的差异就会显示在提交信息之后。比如，执行下面的命令，就可以只查看README.md的提交日志以及提交前后的差异:

		git log -p README.md
		commit 877e12095295286259be9dc69e2eb36fcec54318
		Author: fang feng <fengf1104@gmail.com>
		Date:   Fri Jun 17 13:24:23 2016 +0800

    	First commit

		diff --git a/README.md b/README.md
		new file mode 100644
		index 0000000..e69de29
		
		
#### git diff
git diff命令可以查看 工作树、暂存区、最新提交之间的差异。为了理解git diff命令，我们进行下面的相关操作。

首先，我们在刚刚提交的README.md文件中写点东西。

	#Git教程

执行git diff命令，查看当前工作树与暂存区的区别:

	git diff
	diff --git a/README.md b/README.md
	index e69de29..981aef2 100644
	--- a/README.md
	+++ b/README.md
	@@ -0,0 +1 @@
	+#Git教程
	
这里解释下输出的格式内容。'+'号标出的是新添加的行，被删除的行用'-'表示。

现在用git add命令将修改后的README.md加入暂存区。这个时候执行git diff,由于现在工作树与暂存区不存在任何差别，所以git diff不回输出任何显示。

要查看与最新提交之间的差别，可以执行git diff HEAD命令。

	git diff HEAD
	diff --git a/README.md b/README.md
	index e69de29..981aef2 100644
	--- a/README.md
	+++ b/README.md
	@@ -0,0 +1 @@
	+#Git教程

建议养成这么一个好习惯，在每次提交之间，都通git diff HEAD查看下本次提交与最新提交之间差别，等确认后再进行提交。这里的HEAD是指向当前分支中最新一次的提交。

由于上面我们已经确认了本次提交的内容，可以执行git commit命令进行提交。

	git commit -m "Add index"
	
用git log确认是否已经成功提交:

	git log
	commit 52cc5d0961e614281e5263a8e8b76310321ad696
	Author: fang feng <fengf1104@gmail.com>
	Date:   Sat Jun 18 13:54:01 2016 +0800

    Add index

	commit 877e12095295286259be9dc69e2eb36fcec54318
	Author: fang feng <fengf1104@gmail.com>
	Date:   Fri Jun 17 13:24:23 2016 +0800

    First commit

###3.2 Git分支操作
在进行并行作业时，我们会用到分支。在这类并行开发的过程中，往往会同时存在多个最新代码的我提交。

master是Git默认创建的分支，因此基本上所有开发都是以这个分支为中心进行的。


####git branch
git branch命令可以将分支名列表显示。同时确认当前所在的分支。

	gti branch
	* master

我们可以看到在master分支左侧标有'*'号，这表示我们当前所在的分支。也就是说，我们当前在master分支下进行开发。

####git checkout -b
如果想以当前的master分支为基础创建新的分支，我们需要用到git checkout -b命令。

执行西面的命令，创建并切换到新的分支feature-A：

	git checkout -b feature-A
	Switched to a new branch 'feature-A'
	
实际上git checkout -b feature-A命令等同于下面两条命令:
	
	git branch fearture-A
	git checkout feature-A
	
	
首先用git branch feature-A创建分支，然后再使用git checkout切换到新创建的分支。这时再来看分支列表，会显示我们处于feature-A分支下。

	git branch
	* feature-A
	master

在这个状态下像正常开发那样修改代码，执行git add命令并提交的话，代码就会提交至feature-A分支。像这样不断对对一个分支进行提交的操作，我们称为"**培育分支**"

在feature-A分支中的更改不会影响到master分支，这正是在开发中创建分支的优点。只要创建多个分支，就可以在不互相影响的情况下同时进行多个功能的开发。


####git checkout -
使用连字符'-'代替分支名，就可以切换到上一个分支。


####特性分支
特性分支顾名思义，是集中实现单一特性(主题)，除此之外不进行任何作业的分支。在日常开发中，常常会创建多个特性分支，同时再保留一个随时可以发布软件的稳定分支。稳定分支的角色通常由master分支担当。

####主干分支
主干分支是特性分支的原点，同时也是合并的重点。通常会使用master分支作为主干分支。主干分支中并没有开发到一半的代码，可是随时供他人查看。

有时我们需要让这个主干分支总是配置在正式环境中，有时又需要用标签Tag等创建版本信息，同时管理多个发布版本。拥有多个发布版本时，主干分支也有多个。


####git merge
接下来，我们假设feature-A已经开发完毕，想要将它合并到主干分支mater中。首先切换到master分支:

	git checkout master
	Switched to branch 'master'
	
然后合并feature-A分支。为了在历史记录中明确记录下本次分支合并，我们需要创建合并提交。因此，在合并时加上--no-ff选项:

	git merge --no-ff feature-A
	
随后编辑器会启动，用于录入合并提交的信息。将编辑中显示的内容保存，关闭编辑器，然后就会看到下面的结果:

	Merge made by the 'recursive' strategy.
	README.md | 2 ++
	1 file changed, 2 insertions(+)
	

####git log --graph
用git log --graph命令进行查看的话，能很清楚的看到特性分支(feature-A)提交的内容已被合并。除此之外，特性分支的创建以及合并也都清楚明了。

	git log --graph
	*   commit 195b1158431c32b1b1dc71843f00c9a451d76d29
	|\  Merge: 52cc5d0 8ec81c8
	| | Author: fang feng <fengf1104@gmail.com>
	| | Date:   Sat Jun 18 19:32:47 2016 +0800
	| |
	| |     Merge branch 'feature-A'
	| |
	| * commit 8ec81c83f18371daf3de7fbfb933496fe1c505e2
	|/  Author: fang feng <fengf1104@gmail.com>
	|   Date:   Sat Jun 18 19:09:44 2016 +0800
	|
	|       Add feature-A
	|
	* commit 52cc5d0961e614281e5263a8e8b76310321ad696
	| Author: fang feng <fengf1104@gmail.com>
	| Date:   Sat Jun 18 13:54:01 2016 +0800
	|
	|     Add index
	|
	* commit 877e12095295286259be9dc69e2eb36fcec54318
	  Author: fang feng <fengf1104@gmail.com>
	  Date:   Fri Jun 17 13:24:23 2016 +0800
	
	      First commit
	      
git log --graph命令可以用图表形式输出日志，非常直观，大家务必牢记。

####git reset
要让仓库的HEAD、暂存区、当前工作树回溯到指定状态，需要用到git reset --hard命令。只需要提供目标时间点的哈希值，就可以完全回复到该时间点的状态。

	git reset --hard 52cc5d09
	HEAD is now at 52cc5d0 Add index
	
我们已经成功回溯到创建分支feature-A之前的状态。由于所有文件都回溯到了指定哈希值对应的时间点上，README.md文件的内容也恢复到了当时的状态。

现在我们来创建fix-B分支:

	git checkout -b fix-B
	Switched to new branch 'fix-B'
	
然后我们在README.md中添加一行文字:

	#Git教程
	
	 - fix-B

然后直接提交README.md文件

	git add README.md
	git commit -m "Fix B"
	
现在我们将master分支重新推进到合并feature-A分支后的状态, 我们称这一操作为"推进历史"。


git log命令只能查看以当前状态为重点的历史日志。所以，这里要使用git reflog命令，查看当前仓库的操作历史。在操作历史中找出回溯历史之前的的哈希值，通过git reset --hard命令恢复到回溯历史之前的状态。

首先，执行git reflog命令, 查看当前仓库执行过的操作日志:

	git reflog
	52cc5d0 HEAD@{0}: checkout: moving from fix-B to master
	de74caf HEAD@{1}: commit: Fix B
	52cc5d0 HEAD@{2}: checkout: moving from master to fix-B
	52cc5d0 HEAD@{3}: reset: moving to 52cc5d09
	195b115 HEAD@{4}: checkout: moving from feature-A to master
	8ec81c8 HEAD@{5}: checkout: moving from master to feature-A
	195b115 HEAD@{6}: checkout: moving from feature-A to master
	8ec81c8 HEAD@{7}: checkout: moving from master to feature-A
	195b115 HEAD@{8}: checkout: moving from master to master
	195b115 HEAD@{9}: checkout: moving from master to master
	195b115 HEAD@{10}: checkout: moving from master to master
	195b115 HEAD@{11}: merge feature-A: Merge made by the 'recursive' strategy.
	52cc5d0 HEAD@{12}: checkout: moving from feature-A to master
	8ec81c8 HEAD@{13}: checkout: moving from master to feature-A
	52cc5d0 HEAD@{14}: checkout: moving from feature-A to master
	8ec81c8 HEAD@{15}: checkout: moving from master to feature-A
	52cc5d0 HEAD@{16}: checkout: moving from feature-A to master
	8ec81c8 HEAD@{17}: commit: Add feature-A
	52cc5d0 HEAD@{18}: checkout: moving from master to feature-A
	52cc5d0 HEAD@{19}: checkout: moving from feature-A to master
	52cc5d0 HEAD@{20}: checkout: moving from master to feature-A
	52cc5d0 HEAD@{21}: commit: Add index
	877e120 HEAD@{22}: commit (initial): First commit
	

在日志中，我们可以看到commit, checkout, reset, merge等Git的操作历史记录。只要不进行Git的GC(Garbage Collection,垃圾回收),我们就可以通过日志随意调取近期的历史状态，就像给时间机器指定了一个时间点，在过去未来中自由穿梭一般。


从上面的日志中我们可以看到，合并feature-A分支后的HASH值为195b115,我们将HEAD、暂存区、工作树恢复到这个时间点的状态:

	git checkout master
	git reset --hard 195b115
	HEAD is now at 195b115 Merge branch 'feature-A'
	
现在我们开始合并fix-B分支:

	git merge --no-ff fix-B
	Auto-merging README.md
	CONFLICT (content): Merge conflict in README.md
	Automatic merge failed; fix conflicts and then commit the result.
	

这时，系统告诉我们README.md文件发生了冲突(Conflict)。系统在合并README.md文件时，feature-A分支更改的部分和fix-B分支更改的部分发生了冲突。

不解决冲突就无法完成合并，我们用编辑器打开README.md文件，解决这个冲突。打开README.md文件后，文件内容如下所示:

	#Git教程

	<<<<<<< HEAD
	- feature-A
	=======
	 - fix-B
	>>>>>>> fix-B
	

其中=======以上的部分是当前HEAD的内容，以下的部分是要合并的fix-B分支中的内容。
我们在编辑器中改为我们需要的样子:

	#Git教程

	- feature-A
	- fix-B

冲突解决后，执行git add和git commit命令:

	git add README.MD
	git commit -m "Fix conflict"
	
	
由于本次更改解决了冲突，所以提交信息记录为"Fix Conflict"。












