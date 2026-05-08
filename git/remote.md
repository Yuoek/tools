# remote

<!-- mtoc-start -->

* [git command](#git-command)
* [git remote](#git-remote)

<!-- mtoc-end -->

## git command
添加主仓库
```bash
git remote add origin
```

删除主仓库
```bash
git remote remove origin
```

## git remote

创建目录
```bash
mdkir -p asm
cd asm
```

初始化
```bash
git init
```

添加文件
```bash
echo "# asm" > README.md
```

添加文件
```bash
git add .
```

添加状态 commit
```bash
git commit -m "add asm"
```

切换 main 分支
```bash
git checkout -M main
```

添加主仓库 origin
```bash
git remote add origin git@github.com:Yuoek/asm.git
```

首次推送
```bash
git push -u origin main
```
