# 开发错分支的解决方案
* 4 步教你把开发的导进自己的分支
> 1、暂存改动或者开发的代码
```bash
git add .
```
> 2、把暂存的文件提交到git的暂存栈中
```bash
git stash
```
> 3、切换到你自己的开发分支
```bash
git checkout 分支名
```
> 4、将暂存在暂存栈中的代码吐到当前分支
```bash
git stash pop
```
# 如果你修改了，提交了那就执行下面的操作
> 1、切换到提交错误的分支
```bash
git checkout 错误的分支名
```
> 2、最近一次提交放回暂存区, 并取消此次提交
```bash
git reset HEAD~1
```
> 3、把暂存的文件提交到git的暂存栈中
```bash
git stash
```
> 4、切换到你自己的开发分支
```bash
git checkout 分支名
```
> 5、将暂存在暂存栈中的代码吐到当前分支并继续你的修改或者提交
```bash
git stash pop
```
> 6、最后切回到之前错误的分支回退到上一个版本
```bash
git reset --hard 某个版本的版本号
```

