---
title: 毕业设计之搭建服务器篇（Nginx+PHP以及Nginx静态服务器）
category: way-to-explore
layout: post
---

# 缘起
最近一直在忙毕业设计。其中有一环需要搭建一个静态服务器，用于提供文件上传、下载和以及图片访问。由于一直以来用的都是Nginx，所以也就想到了用Nginx来搭一个静态文件服务器和一个PHP运行环境。这里简要记录下搭建步骤。

关于Nginx的配置文件里的参数的意义，恰好搜到这篇文章《[Nginx配置文件nginx.conf中文详解](http://www.ha97.com/5194.html)》，总结的相当不错。推荐一看。

# Nginx搭建静态文件服务器
在网上搜罗了一圈，相关的文章一大把，讲得既多又杂，但其实只涉及简单的几行配置而已。

打开Nginx的配置文件，默认的是`/etc/nginx/sites-available/default`这个文件。找到server这一块内容，在里面添加以下的配置，相关注释已标注出来，各位看管根据需要自行修改一下就好哈。

    server {
        # 监听来自在所有网络上的80端口的请求
        listen 0.0.0.0:8080;

        # 这个server的根目录
        root /home/sr1/test;

        # ....这里省略其他无关项目

        # 下面的东西是需要自行添加的配置
        location ~ ^/upload/.*\.(png|css|jpg|apk)$ {
            root /home/sr1/files;
            expires 1d;
        }
        # 上面就是需要添加的东西了
        # 对于满足正则表达式 ^/upload/.*\.(png|css|jpg|apk)$ 的url请求，
        # 将其根目录定义为 /home/sr1/files
        # 文件的有效期为一天

        # 这里的效果是，对于满足任意满足”以/upload/“开头，
        # 以png、css、jpg、apk结尾的url请求
        # 将到/home/sr1/files文件夹下去根据这个模式寻找文件，并提供给请求方
    }

设置完上面的之后，重启下Nginx服务器，就生效了。重启的命令是：`sudo service nginx restart`。

# Nginx搭建PHP运行环境
我的Linux系统是LinuxMint，一个基于Ubuntu的衍生版，包管理工具当然是apt-get咯。

因为仅仅是需要PHP的运行环境，因此只需要简单的下载一个`php5-fpm`包就搞定了，安装命令为：`sudo apt-get install php5-fpm`。

之后需要简单的去掉Nginx配置文件里关于php的那几行就好。如下：

    # 同样是在server的区块里
    location ~ .*\.php$ {
    #     fastcgi_split_path_info ^(.+\.php)(/.+)$;
    #   # NOTE: You should have "cgi.fix_pathinfo = 0;" in php.ini
    
    #   # With php5-cgi alone:
        fastcgi_pass 127.0.0.1:9000; # 这一行因人而异
    #   # With php5-fpm:
    #   fastcgi_pass unix:/var/run/php5-fpm.sock; # 也有人说是取消这一行，但这个对我不管用
        fastcgi_index index.php; # 这一行的注释也取消
        include fastcgi_params;  # 还有这行
    }

总共只需要取消以上三行的注释，重启Nginx服务器就好。但看注释，理论上安装的是php-fpm，应该取消注释的是：`fastcgi_pass unix:/var/run/php5-fpm.sock`这一行才对，但我试了很多次都不成功，最后查看了下php的配置文件`/etc/php5/fpm/pool.d/www.conf`，发现`listen = 127.0.0.1:9000`这行，才发现php-fpm模块监听的地址是127.0.0.1:9000，改成了上面那样就能成功运行php程序了。

# 使用PHP发起http请求
php发起网络请求比较麻烦，因为php本身并不具备这样的库，一般是借助curl这个工具来实现的。因此需要先安装curl，以及php-curl开启了这个扩展才能使用。如果不确定自己的php环境是否能够使用curl，可以写一个php页面，内容为：`<?php echo phpinfo(); ?>`看一下输出的信息里有没有curl字段来判断。

如果没有curl或php-curl，就需要安装下他们，命令同样是`sudo apt-get install 包名`，这里我遇到一个很奇葩的事：无法安装php-curl！错误信息是下载失败，奇了怪了。我配置的源是网易的镜像`mirrors.163.com`，怀疑是网易的源的问题，改用了北京化工学院的源`mirror.bit.edu.cn`，执行`sudo apt-get update`之后再安装还是下载失败。主动去源那里找，发现目录里是有这个包，但下载的时候就不行了。最后只能祭出了法宝：百度网盘。使用网盘的离线下载功能，把php-curl的deb包下载下来，输入命令`sudo dpk -i 本地文件`安装，搞定。

在这一步折腾了很久，不知道这个问题到底是什么引起的。只能默默感概，在天朝当程序员实在是太苦逼了。

配置完之后，就能使用curl来发起http请求了。下面是网上找到的一个函数，实测挺好用的。

    function http($url, $method, $postfields = NULL) {
        $ci = curl_init();
        curl_setopt($ci, CURLOPT_URL, $url);
        curl_setopt($ci, CURLOPT_CONNECTTIMEOUT, 30); // 连接超时  
        curl_setopt($ci, CURLOPT_TIMEOUT, 30); // 执行超时  
        curl_setopt($ci, CURLOPT_RETURNTRANSFER, true); // 文件流的形式返回，而不是>直接输出  
        curl_setopt($ci, CURLOPT_ENCODING, "gzip");
        curl_setopt($ci, CURLOPT_HEADER, FALSE);
        if ('POST' == $method) {
            curl_setopt($ci, CURLOPT_POST, true); // post  
        }
        if (!empty($postfields)) {
            curl_setopt($ci, CURLOPT_POSTFIELDS, $postfields); // post数据 可为数组>、连接字串  
        }
        $response = curl_exec($ci);
        if (false) {
            echo "=====post data======rn";
            var_dump($postfields);

            echo '=====info=====' . "rn";
            print_r(curl_getinfo($ci));

            echo '=====$response=====' . "rn";
            print_r($response);
        }
        curl_close($ci);
        return $response;
    }

支持post和get，根据需要传入相应的参数即可。

# 使用php支持文件上传
使用php支持文件上传其实很简单，不知道为什么之前一直觉得难，导致每次想到要做文件上传就心虚。下面是核心的代码：

    function saveUploadFile($saveToPath) {
        if ($_FILES["file"]["error"] > 0) {
            echo $_FILES["file"]["error"];
        } else {

            echo '文件名：' . $_FILES["file"]["name"];
            echo '文件类型：' . $_FILES["file"]["type"];
            echo '文件大小：' . ($_FILES["file"]["size"] / 1024) . 'kB';
            echo '文件临时保存路径：' . ($_FILES["file"]["tmp_name"];

            // 将文件保存到新的目录下，如果不保存的话，临时保存的文件会在执行结束后被自动删除
            if (move_uploaded_file($_FILES["file"]["tmp_name"], $saveToPath.$_FILES["file"]["name"])) {
                echo '保存至：' . $saveToPath.$_FILES["file"]["name"];
            } else {
                echo '保存过程中出现错误，保存失败';
            }
        }
    }

这里可能会因为文件太大而上传失败，爆出来的错误是`413 Request Entity Too Large`，这里需要改下Nginx和php的相关配置，参考这个的指南《[Nginx: 413 Request Entity Too Large Error and Solution](http://www.cyberciti.biz/faq/linux-unix-bsd-nginx-413-request-entity-too-large/)》改下就好了。

# That's all, but not ALL.