## hexo博客管理系统
![](https://img.shields.io/badge/by-hexo-green)     ![](https://img.shields.io/badge/by-Vue-orange)     ![](https://img.shields.io/badge/by-Spring-brightgreen)

**一款可以快速方便管理hexo博客文章、图床的工具。**

**特性**
- 使用了阿里云的对象存储作为图床，以文章标题进行分类；
- markdown语法，粘贴图片即可上传至自定义图床，方便快捷；
- 博客主站配置了阿里云的cdn，发布文章时自动刷新（可选）；
- 方便地管理文章，发布、取消发布、保存草稿；编辑文章时自动保存；

![image](https://user-images.githubusercontent.com/27762147/112250648-b7b3e280-8c94-11eb-8c1a-58bc30ac8ef2.png)
![image](https://user-images.githubusercontent.com/27762147/112250840-1aa57980-8c95-11eb-95d4-9b5f1b447cd0.png)

**原理**

维护 hexo 根目录下 source 文件夹中的 markdown 文件 。

保存草稿时，只保存在数据库中；
发布文章时，在 source 文件夹中写入相应 markdown 文件，调用 shell 部署 hexo，之后刷新 cdn （如果配置了的话）；
取消发布时，移除 source 文件夹中对应的源文件，调用 shell 部署 hexo，之后刷新 cdn （如果配置了的话）；




#### 依赖

- 一台云服务器

- mysql5.7
- 阿里云对象存储账号
- 阿里云cdn（可选）

#### 安装

##### 部署server

项目地址：https://github.com/peihuanhuan/hexo-admin-server
1. 下载server项目代码
```
# 克隆项目
git clone git@github.com:peihuanhuan/hexo-admin-server.git

# 进入项目
cd newblog
```

2. **进入项目后，修改的`src/main/resources/application-example.yml`文件，将hexo路径、mysql、aliyun的服务修改为自己的地址。若没有配置阿里云cdn，删除cdn属性即可**

3. **初始化mysql：执行src/main/resources/static/init.sql，sql中添加了一个admin用户，密码为123456，需自行修改**

4. 最后使用`./gradlew build `命令生成jar包后将build/libs中的jar包复制到自己的服务器中，执行：`nohup java -Dspring.profiles.active=example -server -Xmx1024m -Xms128m -jar newblog-0.0.1.jar &`


##### 部署fe
项目地址：https://github.com/peihuanhuan/hexo-admin-fe
1. 下载fe项目代码，安装依赖
```
# 克隆项目
git clone git@github.com:peihuanhuan/hexo-admin-fe.git

# 进入项目目录
cd vue-element-admin

# 建议不要用 cnpm 安装 会有各种诡异的bug 可以通过如下操作解决 npm 下载速度慢的问题
npm install --registry=https://registry.npm.taobao.org
```

2. **修改 env.production 中的配置，将 `VUE_APP_BASE_API`的值改为自己server部署的地址**

3. 打包 `npm run build:prod`，将根录下的dist文件复制到自己服务器上并配置nginx即可


##### nginx 配置

```nginx
server
{
    # 博客主站，指向 hexo 的 public 文件夹
    server_name your_server_name;
    index index.html;
    root  /www/my-hexo/public;
    access_log  /www/wwwlogs/access.log;
}

server
{
    # 博客管理后台
    server_name your_server_name;
    index index.html;
    root /root/myblog-admin/dist/;
    access_log  /www/wwwlogs/access.log;
}
```

### 更新记录
- 2020-10-8： 新增自定义首页，需在数据库中插入一条id为1的文章，作为首页内容，详见server代码。另外还需要配置hexo。

