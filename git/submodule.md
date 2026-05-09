# Submodule

<!-- mtoc-start -->

* [git command](#git-command)
* [git submodule](#git-submodule)
* [git 切换上一次 commit](#git-切换上一次-commit)
* [submodule 递归 push ](#submodule-递归-push-)

<!-- mtoc-end -->

## git command
初始化
```bash
git init
```

添加
```bash
git add .
```



## git submodule
**克隆主项目**
```bash
git@github.com:Yuoek/Yuoek.git
```

**添加子模块**
```bash
git submodule add git@github.com:Yuoek/typepad.git
```
```bash
git submodule add git@github.com:Yuoek/qwerty-learner.git
```

**主项目初始化并更新子模块**
1.
```bash
 git submodule update --init --recursive
```

```bash

git init
```
```bash
git update
```

或
```bash
git clone --recurse-submodules git@github.com/Yuoek/Yuoek.git
```

**更新子模块**
```bash
git submodule update --remote <子模块路径>
```


**删除子模块**
```bash
git submodule deinit -f tools/git
```
```bash
git rm tools/git
```
```bash
rm -rf .git/modules/tools/git
```
```bash
git add .
```
```bash
git commit -m "remove submodule"
```

**git 子模块修改操作以及更新(tools ai示例)**
1. 进入子模块目录
```bash
cd tools
```

2. 切回 main 分支，解除游离状态
```bash
git checkout main
```

3. 拉取远程最新代码，确保本地分支和远程一致
```bash
git pull origin main
```

4. 修改文件，提交并推送到子模块远程
```bash
git add .
git commit -m "feat: 新增文件/修改功能"
git push origin main  # 这次推送是有效的，因为在 main 分支上
```

5. 回到主仓库
```bash
cd ..
```

6. 更新主仓库里子模块的引用
```bash
git submodule update --remote tools
```

7. 提交主仓库的变更并推送
```bash
git add tools
git commit -m "chore: 更新 tools 子模块引用"
git push origin main
```


## git 切换上一次 commit
查看分支状态
```bash
git status 
```

查看历史提提交
```bash
git reflog
```
or
```bash
git log --online
```

找到上次提交的 commit, 然后修改, example
```bash
git checkout 8f668ec
```

先换分支并合并
```bash
git checkout main
git merge 8f668ec
git push origin main
```

拓展: 假设使用了 add，但没有 commit
```bash
cd tools
git fsck --lost-found
ls .git/lost-found/other/
```


## submodule 递归 push 
子模块操作
```bash
git submodule foreach '
  git checkout main || git checkout master || echo "子模块 $name 无 main/master 分支，跳过"
  git add .
  git commit -m "更新子模块 $name: 提交所有本地修改" || echo "子模块 $name 无修改，跳过提交"
  git push || echo "子模块 $name 推送失败，请手动处理"
'
```

主项目操作
```bash
git add .
git commit -m "更新所有子模块引用"
git push origin main
```
