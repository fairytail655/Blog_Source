---
title: Git使用总结
date: 2021-06-04
categories: 
- Git
tags:
- Git
---
### 环境

- Ubuntu18.04
- Git 2.17.1
- Github

<!--more-->

### 新建项目

1)  本地新建文件夹 <span style='color:blue'>hello_world</span> 及其目录下的 <span style='color:blue'>main.c</span>

   ```shell
   mkdir hello_world
   cd hello_world/
   touch hello_world.c
   ```

2) 初始化本地 Git 项目, 将 <span style='color:blue'>main.c</span> 添加进去并提交到本地仓库

```shell
git init
git add main.c
git commit -m "init"
```

3) 在 GitHub 上新建项目 <span style='color:blue'>hello_world</span> , 使用网页上提供的命令, 将本地仓库推送到云端

```shell
git remote add origin https://github.com/fairytail655/hello_world.git
git branch -M main
git push -u origin main
```

### 提交项目

1) 从 Github 下载项目 <span style='color:blue'>hello_world</span> 到本地

```shell
git clone https://github.com/fairytail655/hello_world.git
cd hello_world/
```

2) 新建并进入分支 <span style='color:blue'>local</span> , 向 Github 提交该分支

```shell
git checkout -b local
git push --set-upstream origin local
```

3) 修改本地 <span style='color:blue'>main.c</span> 文件, 并提交修改, 模拟本地项目开发

```shell
echo "hello world 1" >> main.c
git add main.c # 或者使用 git add * 添加所有修改
git commit -m "push_test1"

echo "hello world 2" >> main.c
git add main.c # 或者使用 git add * 添加所有修改
git commit -m "push_test2"
```

4) 在 Github 上修改分支 <span style='color:blue'>main</span> 下的 <span style='color:blue'>main.c</span> 文件, 模拟同事协作开发

5) 本地切换至分支 <span style='color:blue'>main</span>, 从 Github 下拉最新的修改

```shell
git checkout main
git pull
```

6) 本地切换至分支 <span style='color:blue'>local</span>, 合并之前的 commit 为一个完整的 commit

```shell
git checkout local
git rebase -i HEAD~2
```

7) 合并分支 <span style='color:blue'>main</span>, 并提交到 Github

```shell
git rebase main
git push
```

8) 在 Github 上查看分支 <span style='color:blue'>local</span> 下的 <span style='color:blue'>main.c</span> 文件, 可以发现修改上传成功

### 其他常见操作

1) 查看文件提交状态

```shell
git status
```

2) 查看修改历史

```shell
git log
```

3) 撤销文件修改

```shell
git checkout -- [file_name]
```

