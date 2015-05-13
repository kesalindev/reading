# 常用 git 命令

## checkout
主干：
> git clone ssh://luozhaohui@elastos.org:29418/ElastosRDK4_2_2

分支：
> git clone -b ElastosRDK_r1s ssh://luozhaohui@elastos.org:29418/ElastosRDK4_2_2

## undo
撤消最近一个 commit:
> git reset --soft HEAD^1

撤消 add:
> git undo add

撤消指定文件：
> git reset path_to_file

## 解决冲突
在 <code>git pull --rebase</code> 获得冲突错误之后，解决冲突，然后<code>git add</code>相关的已解决冲突的文件，千万小心不要<code>git commit</code>，而是要<code>git rebase --continue</code>。具体常见如下所示：

> git add -A
> git commit -m "some log"
> git pull --rebase
> error: conflicts...
> fix conflicts
> git add -A
> git rebase --continue
> git push

## 合并
合并分支到主干，假定当前工作在分支上：
> git checkout master
> git pull
> git merge branch_name

