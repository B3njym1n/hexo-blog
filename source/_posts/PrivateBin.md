---
title: PrivateBin
date: 2024-09-12 12:38:41
tags: Software
---

# Table of Contents

1.  [Pastebin替代软件Privatebin](#org819051c)
2.  [安装docker](#org24e1d69)
3.  [拉镜像](#orge3bdef7)
4.  [准备数据卷文件夹和其它](#orgb52a894)
    1.  [`编辑conf/conf.php`](#org6f2a9ef)
5.  [`编辑launch.sh`](#org8f8c573)
6.  [`配置nginx反向代理和HTTPS`](#org82aeb04)
    1.  [安装nginx](#orgae93626)
    2.  [生成证书](#orgf23961b)
    3.  [修改nginx配置](#org57cca51)
        1.  [nginx privatebin设置](#org4db3346)


<a id="org819051c"></a>

# Pastebin替代软件Privatebin

[PrivateBin](https://github.com/PrivateBin/PrivateBin/blob/master/doc/Installation.md) 是一款基于Php开发的代码和文件分析网站，功能和pastebin几乎一样，而且可以自己部署在自己的机器上。


<a id="org24e1d69"></a>

# 安装docker

PrivateBin是用Php开发的，如果你不了解php开发环境配置或者想简化部署步骤还是用docker这样的虚拟化容器比较方便


<a id="orge3bdef7"></a>

# 拉镜像

    docker pull privatebin/nginx-fpm-alpine


<a id="orgb52a894"></a>

# 准备数据卷文件夹和其它

    mkdir -p privatebin
    mkdir -p conf
    mkdir -p data
    chmod -R 777 data


<a id="org6f2a9ef"></a>

## `编辑conf/conf.php`

没有什么特别需求直接套用默认设置

    ;<?php http_response_code(403); /*
    ; config file for PrivateBin
    ;
    ; An explanation of each setting can be find online at https://github.com/PrivateBin/PrivateBin/wiki/Configuration.
    
    [main]
    ; (optional) set a project name to be displayed on the website
    name = "Konrad's PrivateBin"
    
    #页首显示的名字
    
    ; The full URL, with the domain name and directories that point to the
    ; PrivateBin files, including an ending slash (/). This URL is essential to
    ; allow Opengraph images to be displayed on social networks.
    ; basepath = "https://privatebin.example.com/"
    
    ; enable or disable the discussion feature, defaults to true
    discussion = true
    
    ; preselect the discussion feature, defaults to false
    opendiscussion = false
    
    ; enable or disable the password feature, defaults to true
    password = true
    
    ; enable or disable the file upload feature, defaults to false
    fileupload = true
    
    ; preselect the burn-after-reading feature, defaults to false
    burnafterreadingselected = false
    
    ; which display mode to preselect by default, defaults to "plaintext"
    ; make sure the value exists in [formatter_options]
    defaultformatter = "plaintext"
    
    ; (optional) set a syntax highlighting theme, as found in css/prettify/
    syntaxhighlightingtheme = "sons-of-obsidian"
    
    ; size limit per paste or comment in bytes, defaults to 10 Mebibytes
    sizelimit = 10485760
    
    ; template to include, default is "bootstrap" (tpl/bootstrap.php)
    template = "bootstrap"
    
    ; (optional) info text to display
    ; use single, instead of double quotes for HTML attributes
    ;info = "More information on the <a href='https://privatebin.info/'>project page</a>."
    
    ; (optional) notice to display
    ; notice = "Note: This is a test service: Data may be deleted anytime. Kittens will die if you abuse this service."
    
    ; by default PrivateBin will guess the visitors language based on the browsers
    ; settings. Optionally you can enable the language selection menu, which uses
    ; a session cookie to store the choice until the browser is closed.
    languageselection = false
    
    ; set the language your installs defaults to, defaults to English
    ; if this is set and language selection is disabled, this will be the only language
    ; languagedefault = "en"
    
    ; (optional) URL shortener address to offer after a new paste is created.
    ; It is suggested to only use this with self-hosted shorteners as this will leak
    ; the pastes encryption key.
    ; urlshortener = "https://shortener.example.com/api?link="
    
    ; (optional) Let users create a QR code for sharing the paste URL with one click.
    ; It works both when a new paste is created and when you view a paste.
    qrcode = true
    
    ; (optional) Let users send an email sharing the paste URL with one click.
    ; It works both when a new paste is created and when you view a paste.
    ; email = true
    
    ; (optional) IP based icons are a weak mechanism to detect if a comment was from
    ; a different user when the same username was used in a comment. It might get
    ; used to get the IP of a comment poster if the server salt is leaked and a
    ; SHA512 HMAC rainbow table is generated for all (relevant) IPs.
    ; Can be set to one these values:
    ; "none" / "identicon" (default) / "jdenticon" / "vizhash".
    ; icon = "none"
    
    ; Content Security Policy headers allow a website to restrict what sources are
    ; allowed to be accessed in its context. You need to change this if you added
    ; custom scripts from third-party domains to your templates, e.g. tracking
    ; scripts or run your site behind certain DDoS-protection services.
    ; Check the documentation at https://content-security-policy.com/
    ; Notes:
    ; - If you use a bootstrap theme, you can remove the allow-popups from the
    ;   sandbox restrictions.
    ; - By default this disallows to load images from third-party servers, e.g. when
    ;   they are embedded in pastes. If you wish to allow that, you can adjust the
    ;   policy here. See https://github.com/PrivateBin/PrivateBin/wiki/FAQ#why-does-not-it-load-embedded-images
    ;   for details.
    ; - The 'unsafe-eval' is used in two cases; to check if the browser supports
    ;   async functions and display an error if not and for Chrome to enable
    ;   webassembly support (used for zlib compression). You can remove it if Chrome
    ;   doesn't need to be supported and old browsers don't need to be warned.
    cspheader = "default-src 'none'; base-uri 'self'; form-action 'none'; manifest-src 'self'; connect-src * blob:; script-src 'self' 'unsafe-eval'; style-src 'self'; font-src 'self'; frame-ancestors 'none'; img-src 'self' data: https:; media-src blob:; object-src blob:; sandbox allow-same-origin allow-scripts allow-forms allow-popups allow-modals allow-downloads"
    
    #去除`cspheader`前的注释符号，将 cspheader 中的 `img-src 'self' data: blob:` 改为 `img-src 'self' data: https:`
    
    ; stay compatible with PrivateBin Alpha 0.19, less secure
    ; if enabled will use base64.js version 1.7 instead of 2.1.9 and sha1 instead of
    ; sha256 in HMAC for the deletion token
    ; zerobincompatibility = false
    
    ; Enable or disable the warning message when the site is served over an insecure
    ; connection (insecure HTTP instead of HTTPS), defaults to true.
    ; Secure transport methods like Tor and I2P domains are automatically whitelisted.
    ; It is **strongly discouraged** to disable this.
    ; See https://github.com/PrivateBin/PrivateBin/wiki/FAQ#why-does-it-show-me-an-error-about-an-insecure-connection for more information.
    ; httpwarning = true
    
    ; Pick compression algorithm or disable it. Only applies to pastes/comments
    ; created after changing the setting.
    ; Can be set to one these values: "none" / "zlib" (default).
    ; compression = "zlib"
    
    [expire]
    ; expire value that is selected per default
    ; make sure the value exists in [expire_options]
    default = "1day"
    
    [expire_options]
    ; Set each one of these to the number of seconds in the expiration period,
    ; or 0 if it should never expire
    5min = 300
    10min = 600
    1hour = 3600
    1day = 86400
    1week = 604800
    ; Well this is not *exactly* one month, it's 30 days:
    1month = 2592000
    1year = 31536000
    never = 0
    
    [formatter_options]
    ; Set available formatters, their order and their labels
    plaintext = "Plain Text"
    syntaxhighlighting = "Source Code"
    markdown = "Markdown"
    
    [traffic]
    ; time limit between calls from the same IP address in seconds
    ; Set this to 0 to disable rate limiting.
    limit = 10
    
    ; (optional) Set IPs addresses (v4 or v6) or subnets (CIDR) which are exempted
    ; from the rate-limit. Invalid IPs will be ignored. If multiple values are to
    ; be exempted, the list needs to be comma separated. Leave unset to disable
    ; exemptions.
    ; exempted = "1.2.3.4,10.10.10/24"
    
    ; (optional) If you want only some source IP addresses (v4 or v6) or subnets
    ; (CIDR) to be allowed to create pastes, set these here. Invalid IPs will be
    ; ignored. If multiple values are to be exempted, the list needs to be comma
    ; separated. Leave unset to allow anyone to create pastes.
    ; creators = "1.2.3.4,10.10.10/24"
    
    ; (optional) if your website runs behind a reverse proxy or load balancer,
    ; set the HTTP header containing the visitors IP address, i.e. X_FORWARDED_FOR
    ; header = "X_FORWARDED_FOR"
    
    [purge]
    ; minimum time limit between two purgings of expired pastes, it is only
    ; triggered when pastes are created
    ; Set this to 0 to run a purge every time a paste is created.
    limit = 300
    
    ; maximum amount of expired pastes to delete in one purge
    ; Set this to 0 to disable purging. Set it higher, if you are running a large
    ; site
    batchsize = 10
    
    [model]
    ; name of data model class to load and directory for storage
    ; the default model "Filesystem" stores everything in the filesystem
    class = Filesystem
    [model_options]
    dir = PATH "data"
    
    ;[model]
    ; example of a Google Cloud Storage configuration
    ;class = GoogleCloudStorage
    ;[model_options]
    ;bucket = "my-private-bin"
    ;prefix = "pastes"
    ;uniformacl = false
    
    ;[model]
    ; example of DB configuration for MySQL
    ;class = Database
    ;[model_options]
    ;dsn = "mysql:host=localhost;dbname=privatebin;charset=UTF8"
    ;tbl = "privatebin_"	; table prefix
    ;usr = "privatebin"
    ;pwd = "Z3r0P4ss"
    ;opt[12] = true	  ; PDO::ATTR_PERSISTENT
    
    ;[model]
    ; example of DB configuration for SQLite
    ;class = Database
    ;[model_options]
    ;dsn = "sqlite:" PATH "data/db.sq3"
    ;usr = null
    ;pwd = null
    ;opt[12] = true	; PDO::ATTR_PERSISTENT
    
    ;[model]
    ; example of DB configuration for PostgreSQL
    ;class = Database
    ;[model_options]
    ;dsn = "pgsql:host=localhost;dbname=privatebin"
    ;tbl = "privatebin_"     ; table prefix
    ;usr = "privatebin"
    ;pwd = "Z3r0P4ss"
    ;opt[12] = true    ; PDO::ATTR_PERSISTENT
    
    ;[model]
    ; example of S3 configuration for Rados gateway / CEPH
    ;class = S3Storage
    ;[model_options]
    ;region = ""
    ;version = "2006-03-01"
    ;endpoint = "https://s3.my-ceph.invalid"
    ;use_path_style_endpoint = true
    ;bucket = "my-bucket"
    ;accesskey = "my-rados-user"
    ;secretkey = "my-rados-pass"
    
    ;[model]
    ; example of S3 configuration for AWS
    ;class = S3Storage
    ;[model_options]
    ;region = "eu-central-1"
    ;version = "latest"
    ;bucket = "my-bucket"
    ;accesskey = "access key id"
    ;secretkey = "secret access key"
    
    ;[model]
    ; example of S3 configuration for AWS using its SDK default credential provider chain
    ; if relying on environment variables, the AWS SDK will look for the following:
    ; - AWS_ACCESS_KEY_ID
    ; - AWS_SECRET_ACCESS_KEY
    ; - AWS_SESSION_TOKEN (if needed)
    ; for more details, see https://docs.aws.amazon.com/sdk-for-php/v3/developer-guide/guide_credentials.html#default-credential-chain 
    ;class = S3Storage
    ;[model_options]
    ;region = "eu-central-1"
    ;version = "latest"
    ;bucket = "my-bucket"
    
    [yourls]
    ; When using YOURLS as a "urlshortener" config item:
    ; - By default, "urlshortener" will point to the YOURLS API URL, with or without
    ;   credentials, and will be visible in public on the PrivateBin web page.
    ;   Only use this if you allow short URL creation without credentials.
    ; - Alternatively, using the parameters in this section ("signature" and
    ;   "apiurl"), "urlshortener" needs to point to the base URL of your PrivateBin
    ;   instance with "shortenviayourls?link=" appended. For example:
    ;   urlshortener = "${basepath}shortenviayourls?link="
    ;   This URL will in turn call YOURLS on the server side, using the URL from
    ;   "apiurl" and the "access signature" from the "signature" parameters below.
    
    ; (optional) the "signature" (access key) issued by YOURLS for the using account
    ; signature = ""
    ; (optional) the URL of the YOURLS API, called to shorten a PrivateBin URL
    ; apiurl = "https://yourls.example.com/yourls-api.php"


<a id="org8f8c573"></a>

# `编辑launch.sh`

    docker run -d --restart="always" \
          --name=privatebin \
          --read-only \
          -p 34321:8080 \
          -v /home/pepe/privatebin/conf/conf.php:/srv/cfg/conf.php:ro \
          -v /home/pepe/privatebin/data:/srv/data \
          f6bbc444cbbe

写好了之后就不用手打这么长的命令启动privatebin了


<a id="org82aeb04"></a>

# `配置nginx反向代理和HTTPS`

直接访问会遇到安全策略拒绝的情况，因此配置一个支持HTTPS的反向代理服务器，如Nginx或Caddy。这不仅能提升安全性，还能提供更流畅的用户体验。


<a id="orgae93626"></a>

## 安装nginx

    sudo apt install nginx


<a id="orgf23961b"></a>

## 生成证书

    openssl genrsa -out key.pem 2048
    openssl req -new -x509 -key key.pem -out cert.pem -days 365


<a id="org57cca51"></a>

## 修改nginx配置

    sudo vim /etc/nginx/sites-available/privatebin
    sudo ln -s /etc/nginx/sites-available/privatebin /etc/nginx/sites-enabled/
    sudo nginx -t
    sudo systemctl status nginx
    sudo systemctl restart  nginx


<a id="org4db3346"></a>

### nginx privatebin设置

    server {
        listen 443 ssl;
        server_name app.local;
    
        ssl_certificate /home/pepe/privatebin/cert.pem;
        ssl_certificate_key /home/pepe/privatebin/key.pem;
    
        location / {
    	proxy_pass http://127.0.0.1:34321;
    	proxy_set_header Host $host;
    	proxy_set_header X-Real-IP $remote_addr;
        }
    }

通过以上步骤，你就有了一个专属的、可信赖的代码片段分享平台啦！无论是临时保存代码还是跨设备协作，都变得更加轻松愉快。

