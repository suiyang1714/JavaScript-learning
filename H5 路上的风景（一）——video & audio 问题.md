## 引子
> 这几周一直忙着做一个H5动画，说难也不难说简单也不简单。整个动画中穿插着的交互也就一两处，所以一开始想着是让他们把一大段没有交互的动画做成视频，会轻松点，但这无形之中又给自己挖了个坑...
1. video & audio 问题
## 自动播放
众所周知，*苹果* 为了用户着想，禁止了 *Autoplay* 和 *JS "onload"* 加载播放，必须用户主动交互(例如 click)后才能播放 *audio* 和 *video*。但是作为在微信中可以通过监听 *weixinJSBridge* 来解决，很早之前的解决方案一直用到现在还好。



```
<script type="text/javascript" src="http://res.wx.qq.com/open/js/jweixin-1.2.0.js" ></script>  

document.addEventListener("WeixinJSBridgeReady",function () {
	document.getElementById('music2').play();
}, false);

```
*audio* 可以通过上述方案解决，但是 *video* 的自动播放真的行不通，不管是 *ios* 端还是 *android* 端，页面加载完想实现自动播放真的是行不通。只能通过用户的主动触发，比如触摸了屏幕，有 *click* 或 *touch* 事件产生。不知以后  *android* 会不会改进，但至少目前来看是不行的。比较的好的办法是，引导用户触发，滑屏或 *touch *的行为，然后调用 *video.play()* 播放，给用户一个自动播放的错觉。
## video全屏的处理
在H5中插入 *video* 播放时，会遇到不全屏的问题。在 *ios* 端，只需要在 *video* 标签加个 *webkit-playsinline* 和 *playsinline* 属性即可，这两个属性基本上在基于 *webkit* 内核的移动端都是没问题的，此全屏非彼全屏，它是在浏览器视窗内的全屏，并不是占居整个手机的全屏，当然我们做web端需要的就是在 *document* 的 *body* 内的视窗范围的全屏。  
*Android* 端在微信，呵呵...  
100%全屏毋庸置疑，具体情况会在下面说下。
## 预加载问题
对于video或者audio等媒体元素，有一些方法，常用的有 *play()* , *pause()* ;也有一些事件，如'loadstart','canplay','canplaythrough','ended','timeupdate'…..等等。  
这些事件有的能执行，有的不能执行，在不同机子上可能发生N多种情况，比较稳定的事件可能也只有 *ended* 、 *timeupdate* 这两个事件了。比较坑爹的是ios下监听 *canplay* 和 *canplaythrough* （是否已缓冲了足够的数据可以流畅播放）,当加载时是不会触发的，即使 *preload=”auto”* 也没用，但在 pc 的 chrome 调试器下和 android 下，是会在加载阶段就触发。 ios 需要播放后才会触发。  
所以，这样会面临预加载时进度条不会涉及到视频这一块的加载，而且播放后也会有一段时间缓冲和初始化，造成了一段时间的白屏和停顿，导致动画前后的衔接很有问题。在尝试了很多种方法后，可行的解决的方法是：在我们需要播放前提前进行初始化和缓冲—— *play()* ，使视频已经播放初始化。然后在需要播放的的时候再次 *play()* 时可以完美衔接。这种方法暂时的解决了这个问题，但是不确定合不合理，因为看了好多技术贴都没有提及到这一块。 
## android下的播放控件条
按理说在视频播放完毕后会执行 *ended* 事件发生的js程序，但是，但是，但是...  
它却莫名的奇妙的被劫持到qq浏览器的推广视频界面。  
之所以会出现视频推荐，是因为video被强制全屏播放，而且播放完毕后仍然保持全屏。经过测试video的ended事件发生后相关的JS程序还是正常的执行了，但页面表现的所有内容都被这个可恶的全屏video层遮盖了。
经过各种尝试，在 *ios* 端下，一开始给 *video* 添加 *webkit-playsinline* 和 *playsinline* 属性还是生效的，它使视频在页面上原本位置播放，但这个属性在安卓上无效。
对于 *android* 端处理这个问题，讨论了很久也没有什么结果。腾讯一些视频类HTML5，他们都是内链播放，而这个功能需要申请加入所谓的白名单。但是有一种缺不需要白名单也能解决的方案:
```
            <video id="video2" x-webkit-airplay="" webkit-playsinline="" playsinline="" x5-video-player-type="h5" x5-video-orientation="portrait" x5-video-player-fullscreen="true" preload="auto" src="res/video2.mp4" class="IIV" style="width: 100%;height: 100%;object-fit:fill"></video>
            
    x5-video-player-type="h5" 
    x5-video-player-fullscreen="true"
```
这段代码确实可行，它是微信新版 x5 内核支持一个叫 *同层播放器* 的视频播放器，只需要给 *video* 设置上述两个属性即可。方法很完美的解决我测试的安卓机子，  但仅仅支持部分手机，经测试还是有很多安卓机器依然会被劫持。  
此时我已经躺下了，真的折腾不动了。  
现在主流的解决方案应该是放在 *canvas* 里吧，我还没尝试过，因为目前依然在内测，可能明天就会那样改了吧。  

---
> 截止到目前项目还没结束，针对微信H5的视频问题已经焦头烂额，忙里偷闲，写一篇博文冷静冷静。期间也查了很多资料，也知道了这神奇的微信内置浏览器采用腾讯X5内核，而这个坑爹的X5对web标准有很多刻意为之的不遵循，video强制全屏就是其一。这也是腾信自己曾经的一篇公告虽然已经删除了。  
> 总而言之，X5内核以增强用户体验为名，给video套上了自己的UI并强制全屏，这个全屏不接受DOM层面的任何约束，视频播放完毕后还会出现QQ自己的视频推荐，真的堵死一片人。