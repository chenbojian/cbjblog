title: any
tags:
---

## tomcat8配置中注意事项
- servlet-name=default的通常用来处理静态资源,比如会让url-pattern=*.html的资源交给其处理.
- welcome-file-list配置是决定访问以/结尾的url时如何响应,tomcat默认conf/web.xml中配置了index.html等,如果自己项目中没有配置的话就按照默认配置.
- /默认已被welcom-file-list配置处理,写controller时如果RequestMapping到/位置需要注意


## 持续集成工具
- jenkins

## ubuntu字体设置
- ubuntu的字体设置使用fontconfig软件，字体目录位于/usr/share/fonts/，配置目录位于/etc/fonts/
- ubuntu语言选择因为英文时网页大部分字体会变成ukai，而语言选择中文则不会，这是因为在/etc/fonts/conf.d目录下的文件配置了字体到Arial,Serif,Sans-serif,Monospace等font-family的对应关系，语言选择中文时，69-language-selector-zh-cn会起作用，而语言选择英语时，配置文件不起作用，导致网页中用到Arial的中文字体都默认使用了ukai。
- Arial在ubuntu的配置中是映射到Sans-serif的
- 解决办法：添加/etc/fonts/local.conf文件，复制69-language-selector-zh-cn中内容，将只在zh-cn才起作用的项去掉，也可以配置自己喜欢的字体，比如微软雅黑，这样网页就不会再使用ukai了。
