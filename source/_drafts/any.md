---
title: any
tags:
---

## tomcat8配置中注意事项

- servlet-name=default的通常用来处理静态资源,比如会让url-pattern=*.html的资源交给其处理.
- welcome-file-list配置是决定访问以/结尾的url时如何响应,tomcat默认conf/web.xml中配置了index.html等,如果自己项目中没有配置的话就按照默认配置.
- /默认已被welcom-file-list配置处理,写controller时如果RequestMapping到/位置需要注意
- 使用Spring接管/之后静态资源可以通过spring配置mvc:default-servlet-handler来处理,这样不会出现异常错误
- 当Spring接管了/之后,不应该在其前面再配置让default接管*.html之类的来处理静态资源,这样会导致所有的/路径无法调用spring处理,会被default优先处理掉,正确的方法如上,下面是错误和正确的示例.

错误的配置
```xml
<servlet-mapping>
    <servlet-name>default</servlet-name>
    <url-pattern>*.html</url-pattern>
</servlet-mapping>

<servlet-mapping>
    <servlet-name>appServlet</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>

```
这种错误配置的情况,加上一个空的`<welcome-file-list><welcome-file></welcome-file></welcome-file-list>`虽然可以解决/被default接管的问题,但这并不是很好的解决方法.

正确的配置
```xml
<--! web.xml中 -->
<servlet-mapping>
    <servlet-name>appServlet</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>

<--! spring-servlet.xml中 -->
<mvc:default-servlet-handler />
```


## 持续集成工具
- jenkins

## ubuntu字体设置
- ubuntu的字体设置使用fontconfig软件，字体目录位于/usr/share/fonts/，配置目录位于/etc/fonts/
- ubuntu语言选择因为英文时网页大部分字体会变成ukai，而语言选择中文则不会，这是因为在/etc/fonts/conf.d目录下的文件配置了字体到Arial,Serif,Sans-serif,Monospace等font-family的对应关系，语言选择中文时，69-language-selector-zh-cn会起作用，而语言选择英语时，配置文件不起作用，导致网页中用到Arial的中文字体都默认使用了ukai。
- Arial在ubuntu的配置中是映射到Sans-serif的
- 解决办法：添加/etc/fonts/local.conf文件，复制69-language-selector-zh-cn中内容，将只在zh-cn才起作用的项去掉，也可以配置自己喜欢的字体，比如微软雅黑，这样网页就不会再使用ukai了。
