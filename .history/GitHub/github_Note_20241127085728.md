<!--
 * @Author: qinXiong
 * @Date: 2024-11-27 08:50:04
 * @LastEditors: Qxiong&&2307975018@qq.com
 * @LastEditTime: 2024-11-27 08:51:49
 * @Description: 
-->


# github 使用笔记以及问题解决


## 重新生成ssh密钥

- 解决从远程仓库拉取时的问题没有访问权限导致

```git@github.com: Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```
*** 

https://blog.csdn.net/W_317/article/details/106518894

## 正常的代码拉取合并方法
```// 查看远程
git remote -v 
// 获取远程指定分支到本地临时新建的分支
// 获取远程master的分支的代码到临时新建的temp
git fetch origin master:temp
// 查看版本差异
// 查看temp分支与当前分支的差异
git diff temp 
// 将临时分支temp合并到当前分支
git merge tmep
// 删除临时分支
git branch -D temp
```

## 合并分支
```
// 切换到主分支
git checkout main

// 合并分支
git merge origin/分支

//解决冲突问题

//重新上传主分支
git push origin main
```
## 解决克隆重新上传项目到仓库的办法。

git remote set-url origin git@github.com:xiongGithub1/myAutosarNote.git

git push -u origin main

## 图床

### [使用教程][Vscode + Github + Picgo 免费配置markdown图床攻略 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/532669042)

截图剪切到剪贴板，按住ctrl+alt+u就上传到GitHub上。
![20241119141106](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241119141106.png)