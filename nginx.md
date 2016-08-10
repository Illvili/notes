# 平常用到的一些小技巧

## 服务器配置

### 问题
* `fedog` `fis` `gulp` `apache` 等等共存
* 项目中需要使用其他__服务器__/__域名__的服务

### 解决方案
* 将__80__端口提供给`nginx`使用
* 使用__反向代理__来代理所有__服务器__/__API服务__

### 栗子
```
location ~ ^/(static|page|licai-pc|licai-h|yyh5|crm-pc|test|yysc-h5|o2o-crm|licai-app|play) {
    proxy_pass http://127.0.0.1:8080;
}
```
> 罗列了所有的项目名称（可以考虑将`fedog`的sever目录作为`root`）
>
> `http://127.0.0.1/static` -> `http://127.0.0.1:8080/static`
>
> `http://127.0.0.1/yyh5/page/index.html` -> `http://127.0.0.1:8080/yyh5/page/index.html`


```
# yysc-h5 backend API
location ~ ^/installment {
    proxy_pass http://192.168.50.160:8080;
}
```
> 将 `/installment` 目录的调用转发到线上服务器
>
> `http://127.0.0.1/installment/fws/commodity/sku/detail/o/2390131,2435528` -> `http://192.168.50.160:8080/installment/fws/commodity/sku/detail/o/2390131,2435528`


```
# yysc-crm backend API
rewrite ^/mock/(.*)$ /test/$1.json break;
```
> 本地mock数据
>
> `http://127.0.0.1/mock/sys/getMenu` -> `http://127.0.0.1/test/getMenu.json`


```
#o2ocrm
location ~ ^/remote {
    rewrite ^/remote(.*) $1 break;
    proxy_cookie_path / /remote;
    proxy_pass http://o2ocrm;
}
```
> 将 `remote/*` 代理至 `o2ocrm` 流
>
> 同时移除 `/remote` 部分
>
> 保存 `/` 目录cookie 为 `/remote` 路径下
>
> `http://127.0.0.1/remote/sys/getMenu` -> `http://o2ocrm/remote/sys/getMenu`


```
# o2o-crm upstream
upstream o2ocrm {
    server 192.168.49.35:8080;
    server 192.168.49.36:8080;
}
```
> `o2ocrm` 流，和 `server` 配置同层
>
> 可以指定多个源服务器

### 常用命令

#### 安装
```
brew install nginx
```
> 使用 [brew](http://brew.sh/) 来安装`nginx`

#### 配置目录
```
/usr/local/etc/nginx/nginx.conf
```

#### 启动
```
sudo nginx
```
> 因为要开启在80端口所以加上了sudo

#### 检查配置文件
```
nginx -t
```

#### 重载配置文件
```
nginx -s reload
```
