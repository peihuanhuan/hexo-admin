### hexo博客管理后台

项目基于https://github.com/PanJiaChen/vue-admin-template ，能非常方便地管理hexo文章。



特性

- 使用了阿里云的对象存储作为图床

- markdown文本编辑器，复制粘贴上传图片至图床
- 主站配置了阿里云的cdn可自动刷新（可选）
- 编辑文章时自动保存



#### 依赖

- 一台云服务器

- mysql5.7
- 阿里云对象存储账号
- 阿里云cdn（可选）

#### 安装

##### 部署server

```
# 克隆项目
git clone git@github.com:peihuanhuan/hexo-admin-server.git

# 进入项目
cd newblog
```

**进入项目后，修改的`src/main/resources/application-example.yml`文件，将hexo路径、mysql、aliyun的服务修改为自己的地址。若没有配置阿里云cdn，删除cdn属性即可**

**初始化mysql：执行src/main/resources/static/init.sql，sql中添加了一个admin用户，密码为123456，需自行修改**

最后使用` ./gradlew build `命令生成jar包后将build/libs中的jar包复制到自己的服务器中，执行

```
nohup java -Dspring.profiles.active=example -server -Xmx1024m -Xms128m -jar newblog-0.0.1.jar &
```

##### hexo配置
需要进入博客hexo的根目录执行`nohup hexo generate --watch &`命令，使hexo能够监听文件的变化并自动渲染文件到public文件夹


##### 部署fe

```
# 克隆项目
git clone git@github.com:peihuanhuan/hexo-admin-fe.git

# 进入项目目录
cd vue-element-admin

# 建议不要用 cnpm 安装 会有各种诡异的bug 可以通过如下操作解决 npm 下载速度慢的问题
npm install --registry=https://registry.npm.taobao.org
```

**修改 env.production 中的配置，将 `VUE_APP_BASE_API`的值改为自己server部署的地址**

之后打包，将根录下的dist文件复制到自己服务器上并配置nginx即可

```
# 打包
npm run build:prod
```
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
    server_name your_server_name;
    index index.html;
    root /root/myblog-admin/dist/;
    access_log  /www/wwwlogs/access.log;
}
```


