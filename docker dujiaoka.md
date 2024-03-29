# docker快速部署独角数卡

先配置npm把域名反代到`8111`端口，申请证书用`80`端口。

[查看npm部署方法](https://github.com/taotao1058/Docker-Hub/blob/main/docker%20NPM.md#docker%E9%83%A8%E7%BD%B2nginx-proxy-manager)

安装MySQL和Redis：

```
docker run -d -p 172.17.0.1:3306:3306  -e MYSQL_ROOT_PASSWORD=123456 --name mysql -v /data/mysql/config/my.cnf:/etc/mysql/my.cnf -v /data/mysql/db:/var/lib/mysql mysql:5.7
```

```
docker run -d --name myredis -p 172.17.0.1:6379:6379 redis --requirepass "123456"
```

部署独角数卡

```
docker run -dit --name dujiaoka -p 172.17.0.1:8111:80 -p 9000:9000 -e APP_URL=https://域名 -e ADMIN_HTTPS=true -e ADMIN_ROUTE_PREFIX=/admin -e WEB_DOCUMENT_ROOT=/app/public jiangjuhong/dujiaoka:latest
```

输入域名进入安装页面

数据库名称为`mysql`


MySQL和Redis地址都填docker内部IP`172.17.0.1`

密码都是`123456`  可以在部署命令中自行更改


用户名和密码都是`admin`，`admin`

后台路径为`admin`

---
---

## 高阶操作

```
docker exec -it dujiaoka /bin/bash
```

#### 底部页脚路径
```
/app/resources/views/luna/layouts/_footer.blade.php
```

#### 主题路径
```
/app/resources/views
```
#### css样式路径
```
/app/public/assets
```

#### 替换lnua主题背景示例

上传图片到服务器的`home`目录，把上传的图片名称改为`background.png`。

替换：
```
docker cp /home/background.png dujiaoka:/app/public/assets/luna/img/background.png
```

#### 底部运行时间代码
```
<div style="text-align: center; padding: 20px;">
    <span>网站已运行: <span id="adian_time" style="color: #000; font-weight: bold;"></span></span>
</div>

<script>
    function stime() {
        var start = new Date(2023, 9, 5, 12, 0, 0); // 月份是从0开始的，所以5月是4
        var now = new Date();
        var diff = now - start; // 时间差（毫秒）

        var msec = diff;
        var dd = Math.floor(msec / 1000 / 60 / 60 / 24);
        msec -= dd * 1000 * 60 * 60 * 24;
        var hh = Math.floor(msec / 1000 / 60 / 60);
        msec -= hh * 1000 * 60 * 60;
        var mm = Math.floor(msec / 1000 / 60);
        msec -= mm * 1000 * 60;
        var ss = Math.floor(msec / 1000);
        msec -= ss * 1000;

        var dni = dd + "天" + hh + "小时" + mm + "分" + ss + "秒";
        document.getElementById("adian_time").innerHTML = dni;
    }
    setInterval(stime, 1000); // 每秒更新时间
</script>
```
