### Git命令

* git clone

  这是较为简单的一种初始化方式，当你已经有一个远程的Git版本库，只需要在本地克隆一份，例如'git clone git://github.com/someone/some_project.git some_project'命令就是将'git://github.com/someone/some_project.git'这个URL地址的远程版 本库完全克隆到本地some_project目录下面

* git init & git remote

  这种方式稍微复杂一些，当你本地创建了一个工作目录，你可以进入这个目录，使用'git init'命令进行初始化，Git以后就会对该目录下的文件进行版本控制，这时候如果你需要将它放到远程服务器上，可以在远程服务器上创建一个目录，并把 可访问的URL记录下来，此时你就可以利用'git remote add'命令来增加一个远程服务器端，例如'git remote add origin git://github.com/someone/another_project.git'这条命令就会增加URL地址为'git: //github.com/someone/another_project.git'，名称为origin的远程服务器，以后提交代码的时候只需要使用 origin别名即可

* git pull

  从其他的版本库（既可以是远程的也可以是本地的）将代码更新到本地，例如：'git pull origin master'就是将origin这个版本库的代码更新到本地的master主枝，该功能类似于SVN的update

* git add

  是将当前更改或者新增的文件加入到Git的索引中，加入到Git的索引中就表示记入了版本历史中，这也是提交之前所需要执行的一步，例如'git add app/model/user.rb'就会增加app/model/user.rb文件到Git的索引中

* git rm

  从当前的工作空间中和索引中删除文件，例如'git rm app/model/user.rb'

* git commit

  提交当前工作空间的修改内容，类似于SVN的commit命令，例如'git commit -m "story #3, add user model"'，提交的时候必须用-m来输入一条提交信息

* git push

  将本地commit的代码更新到远程版本库中，例如'git push origin'就会将本地的代码更新到名为orgin的远程版本库中

* git log

  查看历史日志

* git revert

  还原一个版本的修改，必须提供一个具体的Git版本号，例如'git revert bbaf6fb5060b4875b18ff9ff637ce118256d6f20'，Git的版本号都是生成的一个哈希值

* git branch
  
  对分支的增、删、查等操作，例如'git branch new_branch'会从当前的工作版本创建一个叫做new_branch的新分支，'git branch -D new_branch'就会强制删除叫做new_branch的分支，'git branch'就会列出本地所有的分支

* git checkout

  Git的checkout有两个作用，其一是在不同的branch之间进行切换，例如'git checkout new_branch'就会切换到new_branch的分支上去；另一个功能是还原代码的作用，例如'git checkout app/model/user.rb'就会将user.rb文件从上一个已提交的版本中更新回来，未提交的内容全部会回滚

### 首次使用
* git init //这是初始化在这个文件夹中建立一个空库
* git add  //这个命令 你可以直接  git add . 这是把当前文件夹中的所有文件都加入到上传的列表中(注意要有空格),你还可以添加具体的文件 git add 你要添加的文件(test/test/test.txt)
* git commit -m "说明"
* git remote add origin https://github.com/test/testt.git  //这里说两处地方  origin 这个相当于是个别名  你可以自己随便写也可以写成当前文件夹的名 , 后面的地址是你在GITHUB 刚刚新建的 库 地址, 你建了哪几个库,你到GITHUB找到 你 建的库点进去 就能看到相应的地址.
* git push -u origin master    //开始上传

### 更新代码
* git add .     或者添加具体的文件 git add 你要添加的文件(test/test/test.txt)
* git commit -m "说明"
* git push -u origin master     //还记的这个别名吗  origin  这个别名就是你用第一种方法首次提交代码你用的别名

### add参数
* git add -A stages All
* git add . stages new and modified, without deleted
* git add -u stages modified and deleted, without new

@end
 
