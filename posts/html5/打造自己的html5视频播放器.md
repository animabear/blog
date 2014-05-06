## 打造自己的html5视频播放器 ##

前段时间重新学习了一下html5的video部分，以前只是停留在标签的使用上，这一次决定深入了解相关的API，并运用这些API打造一个简单的视频播放器。所谓“打造自己的”，就是要自己重写video标签的控制栏部分，实现包括播放、暂停、进度和音量控制、全屏等功能，并自定义控制栏的样式。这是我自己的视频播放器的演示地址（**请用chrome打开**）：  

<http://animademo.sinaapp.com/html5_video/>  （^-^：鼠标中键点击链接，在新标签页中打开）  

这是该播放器的代码地址，也可以下载下来后查看：  
<https://github.com/animabear/html5_video_player>  

下面我将逐步讲解打造自己的html5视频播放器的过程：  

###一、自定义控制栏涉及到的主要API###
1、video播放相关API  

**只读属性：**  
video.duration：整个媒体文件的播放时长，单位s  
video.paused  ：如果媒体文件被暂停，则返回true；如果还没开始播放，默认返回true  
video.ended   ：如果媒体文件播放完毕，则返回true  

**可写属性：**  
video.currentTime：以s为单位返回从开始播放到现在所用的时间。在播放过程中，设置currentTime来进行搜索，并定位到媒体文件的特定位置  
video.volume  ：在0.0到1.0之间设置音频音量的相对值，或者查询当前音量相对值  
video.muted   ：检测当前是否为静音，是则为true；为文件设置静音或消除静音  

**控制函数：**  
video.play()  ：播放视频文件  
video.pause() ：暂停处于播放状态的视频文件  
video.canPlayType() ：测试video元素是否支持给定MIME类型的文件  

**监听事件：**  
ontimeupdate ：当video.currentTime发生改变时触发该事件  

2、全屏控制API  
说明：这里只给出webkit的全屏API，本代码没有做兼容性处理，主要应用了webkit的一些高级API和chrome的伪元素，所以前面请大家用chrome打开演示地址。  

video.webkitRequestFullScreen()：全屏显示  
document.webkitCancelFullScreen()：退出全屏  
document.webkitIsFullScreen：如果当前处于全屏状态，则返回true,否则返回false  
document.addEventListener('webkitfullscreenchange', handler)：当在全屏和非全屏状态切换时，触发该事件  

3、本地文件读取API  
说明：我的这个视频播放器支持从本地添加视频文件播放，支持的格式在webkit浏览器支持的html5视频播放标准范围内。本地文件读取API是html5的新标准。  

window.URL.createObjectURL(file)：file为文件对象，该函数返回指向文件的对象URL，通过该URL可以访问文件。  

    video.src = window.URL.createObjectURL(file);

###二、视频播放器控制栏的样式实现###
为了图方便，布局上我使用 [pure](http://purecss.io/) 来帮忙，一个很简洁的css框架，其实也没用到它多少。至于那些控制按钮，借助css3的@font-face，我使用icon-font来实现。  

icon-font其实就是所谓的图标字体，将设计好的svg格式图片导入相关平台，生成字体文件或者base64的编码字符串，然后在页面中引用这些自定义的字体文件或者插入base64编码字符串。  

这里给大家推荐一个不错的平台，[IcoMoon](http://icomoon.io/#app-features)，借助该平台的[IcoMoon App](http://icomoon.io/app/)，可以方面的完成上述操作。而且该平台还提供了不少优秀的字体库，我使用的就是现有的。对于不怎么会做设计又不想花时间找图片的童鞋来说，这是个不错的选择。其实icon-font主要还是用来减小请求文件大小的。  

###三、video元素的初始化工作###
video元素的html结构：

    <video id="player" width="600" height="450">您的浏览器不支持HTML5
        <source src="./videos/echo-hereweare.mp4"></source>  <!-- 提供默认的播放视频  -->
    </video>
video元素的js初始化：

    var $player = $('#player');
    var player = $player[0];  //方便使用dom原生的api

###四、控制栏上各个控制器的功能实现###

1、播放、暂停和停止  
html:

    <span id="play" class="icon icon-play"></span>
    <span id="stop" class="icon icon-stop"></span>
javascript:

    $play
        .on('click', function() {
            if (player.paused) {
                player.play(); //播放
                $(this).removeClass('icon-play').addClass('icon-pause');
            } else {
                player.pause(); //暂停
                $(this).removeClass('icon-pause').addClass('icon-play');
            }
        });

    $stop
        .on('click', function() {
            player.currentTime = 0; //停止播放
            $innerBar.css('width', 0 + 'px');
        });

2、播放进度控制条和播放时间显示  
1）播放进度控制条：  
这一部分要实现两个功能，一个是点击控制条上的某一点，视频能跳转到对应的时间点；另一个是点击后控制条要有相应的显示（反馈），表明当前位置。  
html:

    <div id="progressBar" class="controlBar">
        <div id="innerBar" class="controlInner"></div>
    </div>
javascript:

    $progressBar
        .on('click', function(e) {
            var w = $(this).width(),
                x = e.offsetX;
            window.per = (x / w).toFixed(3); //全局变量

            var duration = player.duration;
            player.currentTime = (duration * window.per).toFixed(0);

            $innerBar.css('width', x + 'px'); //反馈
        });
进度控制条的实现部分，要用到一个很关键的属性：e.offsetX，在firefox里无此属性，但有一个类似的e.layerX，具体可以查阅MDN。e.offsetX表示鼠标指针的位置相对于触发事件的对象的X坐标，知道了这个值和进度条的宽度，就可以计算出当前点击位置的百分比了，然后就可以根据百分比来重新设置video的currentTime，实现进度控制。

**注意：**这里之所以要引入一个全局变量window.per来记录当前播放的进度百分比，是因为在切换到全屏后，控制条的长度会变长，退出全屏后，控制条的长度又会变短，所以对应的内层进度条（用于显示进度的）的长度也要随之变化，在之后讲实现全屏/非全屏切换时会具体说明。此外，因为在播放过程中这个百分比是变化的，所以也要不断更新window.per这个全局变量：  
javascript:

    $player
        .on('timeupdate', function() {
            // ... (表示省略的代码)
            var w = $progressBar.width();
            if (player.duration) {
                var per = (player.currentTime / player.duration).toFixed(3);
                window.per = per;
            } else {
                per = 0;
            }
            $innerBar.css('width', (w * per).toFixed(0) + 'px');
            // ...
        });
2）播放时间显示  
这个就比较简单了，主要是一个时间换算，还是利用上面的timeupdate事件  
html:

    <span id="timer">0:00</span>
javascript:

    $player
        .on('timeupdate', function() {
            //秒数转换
            var time = player.currentTime.toFixed(1),
                minutes = Math.floor((time / 60) % 60),
                seconds = Math.floor(time % 60);

            if (seconds < 10) {
                seconds = '0' + seconds;
            }
            $timer.text(minutes + ':' + seconds);

            // ... (更新控制条部分)

            if (player.ended) { //播放完毕
                $play.removeClass('icon-pause').addClass('icon-play'); 
            }
        });
**注意：**播放完毕后，记得将播放按钮的图标重置为播放状态  

3、音量控制  
1）静音与取消静音  
html:

    <span id="volume" class="icon icon-volume"></span>
javascript:

    $volume
        .on('click', function() {
            if (player.muted) {
                player.muted = false;
                $(this).removeClass('icon-volume-mute').addClass('icon-volume');
                $volumeInner.css('width', 100 + '%'); //音量控制条回满血
            } else {
                player.muted = true;
                $(this).removeClass('icon-volume').addClass('icon-volume-mute');
                $volumeInner.css('width', 0);
            }
        });
2）音量控制条  
音量控制条和播放进度控制条其实是一样的，唯一不同的是这里我们改变的是video.volume的值。  
html:

    <div id="volume-control" class="controlBar">
        <div id="volume-inner" class="controlInner"></div>
    </div>
javascript:

    $volumeControl
        .on('click', function(e) {
            var w = $(this).width(),
                x = e.offsetX;
            window.vol = (x / w).toFixed(1); //全局变量

            player.volume = window.vol;
            $volumeInner.css('width', x + 'px');
        });
这里设置全局变量的理由同上。  

4、文件上传按钮  
这里要做两件事，一件是上传文件并生成对象URL，另一件是在上传前判断浏览器是否能播放该格式的文件。由于浏览器支持的播放格式比较少，比较常见的就是mp4了，算是一个尝试性功能吧。  
html:

    <span id="upload" class="icon icon-upload"></span>
    <!-- ... 省略代码 -->
    <input type="file" id="file">
css:

    #file {
        visibility: hidden;
    }
javascript:

    $upload
        .on('click', function() {
            $file.trigger('click');
        });

    $file
        .on('change', function(e) {
            var file = e.target.files[0],
                canPlayType = player.canPlayType(file.type); //判断是否支持该格式

            if (canPlayType === 'maybe' || canPlayType === 'probably') {
                src = window.URL.createObjectURL(file);
                player.src = src;
                $play.removeClass('icon-pause').addClass('icon-play'); //新打开的视频处于paused状态
                player.onload = function() {
                    window.URL.revokeObjectURL(src);
                };
            } else {
                alert("浏览器不支持您选择的文件格式");
            }
        });
**注意：**这里为了完全自由定义上传按钮的样式，用了一个小技巧，就是通过点击自定义的上传按钮来触发真正的提交按钮（input[type='file']）的点击事件,然后在css中隐藏真正的提交按钮即可。关于文件读取的API，可以去MDN上详细学习一下。  

5、全屏/非全屏的切换及相关控制  
在全屏和非全屏之间切换，利用webkit的API很容易实现  
html:

    <span id="expand" class="icon icon-expand"></span>
javascript:

    $expand
        .on('click', function() {
            if (!document.webkitIsFullScreen) {
                player.webkitRequestFullScreen(); //全屏
                $(this).removeClass('icon-expand').addClass('icon-contract');
            } else {
                document.webkitCancelFullScreen();
                $(this).removeClass('icon-contract').addClass('icon-expand');
            }
        });
现在有两个比较**关键**的问题，一是如何在全屏时隐藏video标签默认的控制栏并显示自己的控制栏；二是播放进度控制条和音量控制条显示状态的调整，这个在前面已经提到过了。  

1）如何在全屏时隐藏video标签默认的控制栏  
关于这个问题，我刚开始用中文搜了好久，都没有找到相关内容，所以我尝试着在google里用"how to hide video controls in html5"，结果出来的第三条就是我想要的，不得不感慨有些资源只能通过英语才能搜到。  

这篇文章很清楚得描述了这个问题，基本的原理要利用浏览器特有的伪元素，其中还提到了shadow dom这个概念，挺好的一篇文章，我就不赘述了：  
[Hiding Native HTML5 Video Controls in Full-Screen Mode](http://css-tricks.com/custom-controls-in-html5-video-full-screen/)  

2）在全屏/非全屏切换时更改控制进度条（内层进度条）的宽度  
javascript:

    $(document)
        .on('webkitfullscreenchange', function(e) {
            var w = $progressBar.width(),
                w1 = $volumeControl.width();
            if (window.per) {
                $innerBar.css('width', (window.per * w).toFixed(0) + 'px');
            }
            if (window.vol) {
                $volumeInner.css('width', (window.vol * w1).toFixed(0) + 'px')
            }
        });
这里前面定义的两个全局变量就派上用场了。  

<br />
全部的代码可以在github上下载，其实写的是一个很简单的demo，主要目的还是想深入学习一下html5的video，毕竟不能只停留在一个标签的使用上。最后推荐一篇文章，是“打造”自己的HTML5音乐播放器，别人做的那个才是真的牛，很值得学习：  
<http://www.feelcss.com/html5-music-player-design.html>
