## PhantomJS - 前端自动化测试神器

###介绍
说起PhantomJS，不知道大家会不会想起一部叫《幻灵镇魂曲》的动漫，那部作品被简称为“Phantom”，回到正题，我是在公司的前端沙龙上知道PhantomJS的，它是一个基于webkit的“无头浏览器”（headless browser），可以执行JavaScript或CoffeeScript程序。说它是“无头”的，是因为它提供了执行js脚本、进行dom渲染等功能，但不把结果（比如页面）渲染成可视化的内容，这篇文章对“无头浏览器”作了简单明了的解释：  
[What is Headless browser, the list of headless browser and comparison][1]  

在公司的沙龙上，分享人主要围绕自动化测试对PhantomJS展开了介绍，其实除了自动化测试（官网上称为“无头测试”），phantomjs还可以做很多事情，比如屏幕截图、网络监控等，以下是它的官方网站和github地址：  

* [phantomjs.org](http://phantomjs.org/)  
* <https://github.com/ariya/phantomjs>  

下面，我们对它的安装和使用进行简单介绍。

###安装
官网提供了phantomjs在不同系统环境下的下载地址：<http://phantomjs.org/download.html>，不过需要自备梯子，不知道怎么搭梯子的可以google一下goagent。这里以windows为例，这是windows下的[**安装文件**](http://pan.baidu.com/s/1gdJxJvP)，我把它放到百度网盘上了。  

安装其实很简单，直接把下载下来的压缩包解压到本地目录，然后把phantomjs.exe的路径加入环境变量，之后就可以直接在命令行执行phantomjs命令了。在命名行输入以下命令检测是否安装成功：

    phantomjs --version

若成功则会打印出版本号。  

解压后的压缩包里面有个名为example的文件夹，里面有很多例子，可以直接运行。

###使用
比如你编写了一个hello.js的文件，文件内容如下：

    console.log('Hello, world!');
    phantom.exit();

那么在命令行输入以下命令即可执行该文件：

    phantomjs hello.js

输入结果如下：
    
    Hello, world!

用该命令也可以执行CoffeeScript的文件。  

至于具体如何编写phantomjs的文件、如何使用phantomjs的功能，最好的学习方法是还看官方文档，我也就不多介绍了。  

官方文档：<http://phantomjs.org/documentation/>  

之后如果在使用中有了什么心得，再来分享~


[1]: http://www.zenmeo.com/wordpress/what-is-headless-browser-the-list-of-headless-browser/
