### Ubuntu 下使用 Meld Diff Viewer 作为默认的 git diff 工具
Meld Diff Viewer 是一款非常好用的可视化版本比较工具，Ubuntu 下也可以使用，可以完美地与 git 组合使用。

* 下 载：
``` shell
$ sudo apt-get update && sudo apt-get install meld
```
* 新建名为 git- diff.sh 的脚本文件：
``` shell
#!/bin/bash
meld $2 $5 > /dev/null 2>&1
```
* 将该脚本拷贝到  /usr/local/bin/
``` shell
$ sudo mv git-diff.sh /usr/local/bin/
$ sudo chmod +x /usr/local/bin/git-diff.sh
```
* 用文本编辑器打开 git 配置文件：$HOME/.gitconfig，在最后添加：
``` shell
[diff]
     external = /usr/local/bin/git-diff.sh
```
* 大功告成！可以使用如下命令比较版本：

<code>git diff .</code>     => 比较当前目录下的修改

<code>git diff commit1 commit2</code>  => 两次 commit 之间的比较

<code>git diff commit filename</code> => 当前版本的 filename 文件与指定 commit 版本的比较

* 此外，meld diff viewer 也可以单独作为可视化比较工具，比较两个文件或两个目录，在此就不再累述了。
