---
title: 为什么我的chrome总是自动安装插件
date: 2018-11-07 14:48:01
tags:
---

最近用mac的chrome遇到了一个很烦人的问题，每次新建一个profile的时候，chrome都会提示自动安装了fuze，记录一下解决方案。

我开始的时候用google搜索了很久都没有找到相应的解决方案，甚至研究了chrome的一些配置，但是太过复杂，不得不放弃。

后来终于在Google Chrome Help Forum搜索fuze得到了这样一个[问题](https://productforums.google.com/forum/#!topic/chrome/mr3ECzJcspA)

>Hello,
>
>I installed a Chrome extension called 'Fuze on Chrome' in October 2016. I've tried to delete it through Chrome Extensions Settings, however, every time I create a new Chrome profile on this computer, Fuze on Chrome automatically installs. I can't find Fuze on Chrome in the Chrome Web Store anymore. How do I delete this extension from my local machine for good?
>
>Other funky behavior:
>1. In 'chrome://extensions', when I click Trash Icon > Check the checkbox for Report Abuse  > Click 'Remove' , I receive a 404 error and I am redirected to this URL: https://chrome.google.com/webstore/report/kcehcblfpidimbihdfophhhdejckolgh?utm_source=chrome-remove-extension-dialog. 
>
>2. I cannot find the folder with its corresponding Developer ID under this directory: ~/Library/Application Support/Google/Chrome/Default/Extensions
>
>3. I tried deleting all key references of the developer id and its corresponding values within the ~/Library/Application Support/Google/Chrome/Default/Preferences file, but it somehow gets populated again after I rewrite it. (Apologies if this is standard behavior, I'm not familiar with the Chrome Extension development process).
>
>4. I've tried deleting the Chrome application from my system and reinstalling it, but Fuze automatically gets reinstalled. It is also showing up when I reinstall Chrome Canary. 
>
>
>Any assistance would be greatly appreciated!
>
>Thanks,
>S


答案是

> Hi, I figured out to delete it. Go into:
> 
> "/Library/Application Support/Google/Chrome/External Extensions"
> 
> and delete the file called: "kcehcblfpidimbihdfophhhdejckolgh.json"

这个目录是chrome提供的自动安装extension的一种方式，详细可以参考[官方文档](https://developer.chrome.com/apps/external_extensions)