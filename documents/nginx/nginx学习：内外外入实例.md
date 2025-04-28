# nginx学习：内外外入实例



> 需求实现
>
> + 实现内网外入，即外网网址可以跳转访问内网
> + 限制访问范围
> + 防止恶意爬虫

## NGINX基础结构

**http模块**

```nginx
server {
		listen 80;
    #匹配服务器
		server_name ~^(.+)\.cqu\.pt$;

		resolver 202.202.32.33 114.114.114.114 8.8.8.8 valid=3600s;
		set $origin $1;
		set $cqupt $1;

		error_page 403 https://cqu.pt/403.html#$cqupt;

		include conf.d/cqu.pt.rule;

		...#其他配置
        
}
```

> server_name

用于配置基于名称的虚拟主机，匹配请求的域名或网址。如果匹配到，则进入该server的作用域

+ 如果一直都没有匹配到  进入默认server， 即最先匹配到的server

  也可以手动默认

  ```nginx
  {
    listen 80 default;
    server_name _;
    
    ...
  }
  ```

+ server_name可以匹配域名也可以匹配IP地址

+ 可以使用负载均衡



> error_page 

**没有"="号**

`error_page 403 https://cqu.pt/403.html#$cqupt;` 设置403重定向

重定向的地址可以是本地URL,最好写一个location域定位

```nginx
 error_page 400 401 402 403 404 405 408 410 412 413 414 415 500 501 502 503 504 506 /404.html;
    location = /404.html {
        #放错误页面的目录路径。
        root /root/learning_system_back_v2.0/public;
    }
```



**存在"="号 **

自定义错误码,后面的URL可以指向动态也可以指向静态

```nginx 
error_page 502 503 =200 /50x.html;
# error_page 404 = /404.php;
```

error_page在一次请求中只能响应一次

对应的nginx有另外一个配置可以控制这个选项：recursive_error_pages 默认为false，作用是控制error_page能否在一次请求中触发多次。 

> resolver

用于匹配解析域名的DNS地址



> include

导入其他配置





**rewrite模块**

> server_name ~^(.+)\.cqu\.pt$;
>
> set set  \$origin  \$1;

`set`编辑变量$origin

`$1`上一个正则表达是匹配到的参数

这里将内网原始域名匹配出来





## 核心代理

```nginx
location / {
		proxy_pass http://$cqupt;
		proxy_redirect off;
		proxy_set_header Host $cqupt;
		proxy_set_header Referer http://$cqupt$request_uri;
		proxy_set_header Origin	http://$cqupt;
		proxy_set_header X-Real-IP '';
		proxy_set_header X-Forwarded-For '';
		# 第三方模块 nginx_substitutions_filter
		subs_filter_types text/javascript application/javascript;
		subs_filter $cqupt $origin.cqu.pt ig;
		subs_filter '</body>' '<script src="//cqu.pt/run.js" type="text/javascript" charset="utf-8"></script></body>' ig;
	}
```





​	

## 对请求的限制

```nginx

		# 禁止UserAgent非浏览器的问题
		if ($http_user_agent ~* (Scrapy|Curl|HttpClient)) {
		return 403;
	}
	# 禁止指定UA及UA为空的访问
	if ($http_user_agent ~ "qihoobot|Baiduspider|360Spider|bingbot|Googlebot|Googlebot-Mobile|Googlebot-Image|Feedfetcher-Google|Yahoo! Slurp|Yahoo! Slurp China|YoudaoBot|Sosospider|Sogou spider|Sogou web spider|MSNBot|ia_archiver|Tomato Bot|FeedDemon|JikeSpider|Indy Library|Alexa Toolbar|AskTbFXTV|AhrefsBot|CrawlDaddy|CoolpadWebkit|Java|Feedly|UniversalFeedParser|ApacheBench|Microsoft URL Control|Swiftbot|ZmEu|oBot|jaunty|Python-urllib|lightDeckReports Bot|YYSpider|DigExt|HttpClient|MJ12bot|heritrix|EasouSpider|Ezooms|^$" ) {
		return 403;
	}
	# 禁止非GET|HEAD|POST方式的抓取
	if ($request_method !~ ^(GET|HEAD|POST)$) {
		return 403;
	}
```







## 第三方模块

> nginx_substitutions_filter

