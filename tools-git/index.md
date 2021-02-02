# Git常用命令


记录Git的常用命令
<!--more-->

### svn

* 版本回滚
    
    > svn revert -R .

* 版本比较

    > svn diff

* 比较两个目录

    ```
    svn diff </path> </path2>
    svn diff svn://106.75.122.25/nextfintech/source/trading_center/prod/tradingcenter_parent
    svn://106.75.122.25/nextfintech/source/trading_center/trunk/tradingcenter_parent >> diff.txt
    grep Index diff.txt
    # 以上为修改的文件
    ```
    
### git

* 创建本地仓库

    ```
    git init
    git --help
    git add ./
    git commit -m "first commit"
    git config --global user.email "1014435409@qq.com"
    git config --global user.name "EmbraceUU"
    git commit -m "first commit"
    git remote add origin https://github.com/EmbraceUU/go-commons.git
    git pull --rebase origin master
    git push -u origin master
    ```
    
* 删除远程库文件

    ```
    git rm --cached -r .idea
    git commit -m 'delete .idea'
    git push origin master
    ```

* error

    > error: Your local changes to the following files would be overwritten by merge:
    
    ```
    git reset --hard
    git pull origin master 
    ```
    
* 回退commit版本

    ```
    git log  查看日志
    git reset --soft <版本号>
    只回退commit, 不会修改index和缓冲区
    ```
