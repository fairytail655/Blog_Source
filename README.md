# Blog_Source
 博客的源框架

1、npm install hexo-cli -g  
2、hexo init[生成框架文件]  
3、修改_config.yml文件:  
   deploy:  
     type: git  
     repository: 你的github项目地址  
     branch: master  
4、npm install --save hexo-deployer-git  
hexo s -g —— 开启本地服务器  
hexo d -g —— 博客部署到云端  
