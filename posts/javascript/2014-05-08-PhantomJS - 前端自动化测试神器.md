## PhantomJS - 前端自动化测试神器

###介绍
说起PhantomJS，不知道大家会不会想起一部叫《幻灵镇魂曲》的动漫，那部作品被简称为“Phantom”，回到正
题，我是在公司的前端沙龙上知道PhantomJS的，它是一个基于webkit的“无头浏览器”（headless browser），可以执行JavaScript或CoffeeScript程序。说它是“无头”的，是因为它提供了执行js脚本、进行dom渲染等功能，但不把结果（比如页面）渲染成可视化的内容，这篇文章对“无头浏览器”作了简单明了的解释：  
[What is Headless browser, the list of headless browser and comparison][1]  

在公司的沙龙上，分享人主要围绕自动化测试对PhantomJS展开了介绍，其实除了自动化测试（官网上称为“无头测试”），Phantomjs还可以做很多事情，比如屏幕截图、网络监控等，以下是它的官方网站和github地址：  
[phantomjs.org](http://phantomjs.org/)  
<https://github.com/ariya/phantomjs>  

下面，我们对它的安装和使用进行简单介绍。

###安装

window下载后解压，把phantomjs.exe的路径加入环境变量

###使用  

[1]: http://www.zenmeo.com/wordpress/what-is-headless-browser-the-list-of-headless-browser/
