# 使用 connect-cas 的 nodejs web demo

### 关于这个Demo

这个demo是fork [nodejs-cas-client-demo](https://github.com/leleuj/nodejs-cas-client-demo) 这个项目的，该项目使用 [connect-cas](https://github.com/AceMetrix/connect-cas/) 作为connect/express的中间件方便node的web应用使用 [cas](https://www.apereo.org/projects/cas) 统一身份认证

在nodejs应用中使用cas，还可以参考 [cas-authentication](https://github.com/kylepixel/cas-authentication)

### 主要配置

1. cas配置

    ```
    cas.configure({
        protocol: 'http',
        host: 'ids.ynu.edu.cn',
        port: 80,
        paths: {
            validate: '/authserver/validate',
            serviceValidate: '/authserver/serviceValidate',
            login: '/authserver/login',
            logout: '/authserver/logout'
        }
    });
    ```

2. 核心代码，详见routes/default.js中的`/protected/index`路由处理

    ```
    // cas.ssout                    : handle logout requests directly from the CAS server
    // cas.serviceValidate()        : validate service tickets received from the CAS server
    // cas.authenticate()           : request an authentication if the user is not authenticated
    router.get('/protected/index', cas.ssout('/protected/index'), cas.serviceValidate(), cas.authenticate(), function(req, res) {
      res.render('protected', {user: getUser(req), attributes: getAttributes(req)});
    });
    ```

3. 客户端域名、IP绑定

    此测试客户端在服务端设置的域名及端口分别是 **http**://**cas.test.ynu.edu.cn**:**8080**，为了方便本地开发，可以在/etc/hosts文件中添加本地的DNS解析记录`127.0.0.1    cas.test.ynu.edu.cn`，并且注意设置nodejs的web应用的监听地址为`0.0.0.0`，端口为`8080`

### 如何运行

1. 下载代码

    ```
    git clone https://github.com/ynu/nodejs-cas-client-demo.git
    ```

2. 安装nodejs的依赖库（需要安装[nodejs](http://nodejs.org/)）

    ```
    cd nodejs-cas-client-demo
    npm install
    ```

3. 运行nodejs web应用

    ```
    npm start
    ```

4. 操作流程

	在浏览器中访问`http://cas.test.ynu.edu.cn:8080/`，点击`Call the /protected/index page`链接访问受保护的资源，这时候会自动跳转到cas统一身份认证页面，使用用户名/密码方式登陆后，又会自动跳转回来，并且会附带用户信息如下

    ```
    User: ldh
    Attributes: {"containerId":["uid=ldh,ou=cas,ou=test,ou=People"]}
    ```

    在主页上点击`Call the CAS logout`链接即可注销
