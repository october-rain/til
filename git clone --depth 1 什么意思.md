如果你不需要参与到开发，而是想要学习最新的源码，或者当目过大，git clone 可能出现一些问题。

这种情况就可以用 `git clone --depth 1` 解决

depth用于指定克隆深度，为1即表示只克隆最近一次 `commit` .

这种方法克隆的项目只包含最近的一次 `commit` 的一个分支，体积很小

但会产生一个问题，他只会把默认分支 `clone` 下来，其他远程分支并不在本地，所以这种情况下，需要用如下方法拉取其他分支：

```sh
$ git clone --depth 1 https://github.com/dogescript/xxxxxxx.git
$ git remote set-branches origin 'remote_branch_name'
$ git fetch --depth 1 origin remote_branch_name
$ git checkout remote_branch_name
```