### svn
    删除SVN版本信息 find . -type d -name ".svn"|xargs rm -rf
    创建SVN目录 svnadmin create /Users/larry/Repositories
    启动SVN服务 svnserve -d -r /Users/larry/Repositories
    创建SVN目录 svnadmin create /Users/larry/Repositories 
    批量添加文件 svn st |grep ? |awk '{print $2}' |xargs svn add

