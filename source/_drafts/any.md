title: any
tags:
---

## tomcat8配置中注意事项

- servlet-name=default的通常用来处理静态资源,比如会让url-pattern=*.html的资源交给其处理.
- welcome-file-list配置是决定访问以/结尾的url时如何响应,tomcat默认conf/web.xml中配置了index.html等,如果自己项目中没有配置的话就按照默认配置.
- /默认已被welcom-file-list配置处理,写controller时如果RequestMapping到/位置需要注意
