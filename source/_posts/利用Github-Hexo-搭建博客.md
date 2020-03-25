---
title: 利用Github + Hexo 搭建博客
date: 2020-03-19 23:06:21
tags: Hexo
---

搭建当前博客过程中，遇到了些许的坑，记录在此。

1. 注册 Github.com 账号，并创建一个 {username}.github.io 的仓库。

2. 本地配置好 git 账户，在Github上添加 ssh key。

3. 下载安装 Node.js 。下载地址：https://nodejs.org/zh-cn/download/

4. 安装 Hexo

   ```
   npm install hexo-cli -g
   ```

5. 初始化博客项目，创建好一个项目目录，在项目目录中

   ```
   hexo init
   ```

6. 将 Hexo 编译成 HTML 代码

   ```
   hexo generate  # 缩写 hexo g
   ```

7. 启动本地服务器

   ```
   hexo server  # 缩写 hexo s
   ```

8. 本体运行没错误的话，下一步，部署在 Github Pages 上。

9. 配置根目录下 _config.yml 文件

   ```
   # Deployment
   ## Docs: https://hexo.io/docs/deployment.html
   deploy:
     type: git
     repo: git@github.com:{username}/{username}.github.io.git
     branch: master
   ```

10. 安装 Git 部署插件

    ```
    npm install hexo-deployer-git --save
    ```

11. 执行 Hexo 部署命令

    ```
    hexo deploy  # 缩写 hexo d
    ```

12. 打开 username.github.io 确认是否部署成功

13. 部署成功后，会在 username.github.io 的仓库中，看到博客的源代码在 master 分支上

14. 在本地创建 git 仓库，将 Hexo 的项目源代码上传到 Github中，也在 username.github.io 这个仓库中，使用 source 分支。

    ```
    git init
    git checkout -b source
    git add -A
    git commit -m "init blog"
    git remote add origin git@github.com:{username}/{username}.github.io.git
    git push origin source
    ```

15. 配置站点信息，根目录的 _config.yml 文件。其中 language 配置为 zh-CN，timezone 配置为 Asia/Shanghai 。

16. 修改主题，使用 Next 主题

17. 在 Github 网站上 fork Next 的主题到自己的仓库中，这样便于修改主题的配置文件后，也保存在 Github 中。

18. 使用 submodule 添加自己 forked 的 Next 主题仓库

    ```
    git submodule add git@github.com:{useranme}/hexo-theme-next.git themes/next
    ```

19. 使用 Next 主题，修改根目录下的 _config.yml 文件

    ```
    theme: next
    ```

20. 根据需要配置 Next 的主题配置，在 /themes/next 文件夹下的 _config.yml 文件中。

21. Hexo 写新的文章

    ```
    hexo new article-title
    ```

22. Hexo 博客项目文件修改配置或新增文章后，需要重新部署

    ```
    hexo clean
    hexo g
    hexo d
    ```

23. 在 Github 的 username.github.io 仓库中配置自己的域名。在 Hexo 部署的时候，会自动清除点自己配置的域名信息，需要在项目的根目录下创建一个 CNMAE 文件，内容为

    ```
    xxx.com
    ```

24. 上传主题配置文件到 Github，在主题文件目录内

    ```
    git add .
    git commit -m "xxx"
    git push
    ```

24. 上传项目文件到 Github，在项目跟目录下

    ```
    git add .
    git commit -m "xxx"
    git push origin source
    ```



参考：https://cuiqingcai.com/7625.html