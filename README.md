 

移动端web开发基本上分为三种：


移动端web app开发
简单来说就是在开发中使用一些浏览器私有的方法，使得web页面拥有一些native的功能。

hybrid app开发
简单来说就是通过写特定的代码生成跨平台的web app，类似react，phonegap，cocos2d等。

由于本身没有深入移动端开发，但也可以预见一些移动端开发会碰到的问题：

css与js跨平台的问题
h5及诸多新特性的使用
响应式布局及屏幕分辨率问题
native交互的使用
调试方法
性能优化
等等
当前移动设备的市场，主要考虑android，ios，windows三大平台就可以了。国内的话注意本身机器是可能安装有QQ，UC等浏览器的，还要考虑这方面的支持。在此入门只考虑webkit内核就可以了。

接着，针对于前两种开发模式，还是有必要了解一下移动端浏览器关于viewport的概念！

CSS像素与设备像素
设备像素（screen.width/height，单位是设备像素）实际上就是物理像素，衡量屏幕的分辨率实际上就是设备像素的多少，而CSS像素是一种抽象的宽度衡量。

让我们举一个关于缩放的栗子，浏览器实现缩放的原理实际上就是通过拉伸像素来实现的： 假设现在整个屏幕来展现一个CSS像素宽度为128px的元素，屏幕的设备像素宽度是128px，那么实际上1个CSS像素等于1个设备像素，

现在我们将元素放大为原来的200%，那么1个CSS像素等于4个设备像素，如图（一个红色小方块等于一个CSS像素）：

设备像素对于开发来说基本上没用，缩放比例对你来说也没有什么影响，CSS会将展示效果处理的很好，但理解这个概念对接下来讲的会有所帮助，接下来很多度量是以CSS像素为单位的。

PC端的viewport
viewport的功能是浏览器布局实现中用来约束网页中最顶级快级元素<html>的。

让我们举个例子，大家都知道我们使用流式布局时给某个元素设置【width: 10%】的属性，那么它的宽度就是其父元素宽度的10%，假设是<body>元素，那么问题就变成了<body>元素的宽度是多少呢？按照刚刚的理论，<body>元素的宽度是<html>元素的宽度的100%，我们又知道<html>元素的宽度是浏览器窗口的宽度。但理论上<html>的宽度等于viewport的宽度的100%，viewport实际上等于浏览器窗口。

是的，它就是这么定义的，就这么抽象地去理解它。viewport不是一个HTML结构，所以你不能用CSS来控制它。

浏览器的这一性质会带来一些影响，比如我们的导航条的宽度设置成100%，那么当页面放大的时候，就会出现下面的状态：



这样我们知道viewport实际上就是浏览器窗口，它的大小是由浏览器特性所决定的，一旦页面渲染完成，无所是缩放操作还是其他什么操作都不会让它变化。 那么我们可以用<html>元素来度量它，熟悉DOM的人都知道，document.documentElement实际上指的就是<html>元素，但document.documentElement.clientWidth/Height就是viewport的大小。不管html尺寸是多少（也许你会在css或者是html中给<html>元素附上width属性，度量<html>元素大小的属性是document.documentElement.offsetWidth/Height）。这里的度量是用CSS像素来度量的。

移动端的viewport
我们想象一下照搬PC端的模型迁移到移动端来展示，那么假设一个设备屏幕为400px的设备，展示一个流式布局的页面，宽度为10%的列将会被压缩到窄窄的一条，在手机上就会失去展示效果。所以移动端的viewport会比PC端的稍微复杂一点，分为layout viewport和visual viewport。

我们先来看看stackoverflow上George Cummins大神给出概念的定义：

Imagine the layout viewport as being a large image which does not change size or shape. Now image you have a smaller frame through which you look at the large image. The small frame is surrounded by opaque material which obscures your view of all but a portion of the large image. The portion of the large image that you can see through the frame is the visual viewport. You can back away from the large image while holding your frame (zoom out) to see the entire image at once, or you can move closer (zoom in) to see only a portion. You can also change the orientation of the frame, but the size and shape of the large image (layout viewport) never changes.

解释一下就是，visual viewport是页面当前显示在屏幕上的部分，用户可以通过滚动来改变他所看到的页面部分，或者通过缩放来改变visual viewport的大小。layout viewport就是一个页面渲染之后具有固定大小的大框，跟之前提到PC上的概念相似，我们知道它的大小是由浏览器特性来决定的，在PC端就是等于窗口大小，但在移动端不同浏览器的值不同，比如Safari的值是980px，Android WebKit为800px。我们通过两幅图和一个栗子感受一下：

 

可以看到，当缩放比例为100%时，layout viewport = visual viewport，当用户将页面放大时，显示在屏幕上的页面部分变化了，所以visual viewport变化了，而layout viewport的大小还是原来的大小（这里以CSS像素单位来理解）

度量visual viewport是通过window.innerWidth/Height来度量的，当然单位也是CSS像素。

Meta viewport标签
这个标签实际上就是向HTML文件中插入如下句式的标签，起初起源于Apple：

<meta name="viewport" content="width=device-width, initial-scale=1.0">
可以看看MDN上的文档。

它的作用是调整layout viewport的宽度（或者其他，参见文档），那么这里解释一下为什么ppk大神认为移动端上实际还有另一个viewport叫做ideal viewport。就是理想状态下的viewport大小，可以让页面上的元素最好地展示。在上面那条标签中，顾名思义width=device-width是将layout viewport的宽度设置为屏幕的宽度，但这里有些隐情就是比如当使用device-width时，Nexus One的正规宽度是480px，Google的工程师们会返回给你一个320px的宽度，因为原本的太大了。

这里值得强调的几点还有：

1.【width=device-width】或者【initial-scale=1】会将layout viewport设为ideal viewport的大小

2.所有的scale指令都是相对于ideal viewport的，不管layout viewport的大小是多少。 【zoom factor = ideal viewport width / visual viewport width】

3.initial-scale指令通常会这么做：

设置页面初始的zoom factor，计算出visual viewport的大小
设置页面的layout viewport的大小为刚刚计算出的visual viewport的大小
4.当initial-scale指令与width指令冲突时，浏览器的行为

5.未设置meta viewport时，页面怎样显示

总而言之，在我们进行移动端web开发的时候将

<meta name="viewport" content="width=device-width, initial-scale=1">
标签加入到页面就可以了。

再接着，要了解移动端页面的布局方式（流式布局，响应式布局与REM）移动端web页面布局的方式：

px
我们都知道传统的pc端页面布局都是将最外层的container设置为980px或者1080px这样。当浏览器窗口缩小时，内容会被剪掉，实际上由于viewport的原因，这种固定大小的页面在移动端的展示也是很不友好的。

%（流式布局）
流式布局实际上就是百分比宽度 + 固定高度，当前国内百度的移动端页面就是这么做的。当浏览器宽度缩小，容器也跟着缩小，当设备屏幕较小时，体验也会好一点。但是流式布局也还是会有问题，比如iphone 6跟iphone 4的屏幕大小不是一样的，虽然元素宽度是百分比的，但是会存在文字会减行，px单位的border-radius放大后失效，图片长宽比出现变化等问题。当然部分问题可以通过百分比相对于父元素宽度的css属性，例如padding等，来解决（也可以解决因图片未加载完成到加载完成渲染时导致图片下面的元素重排的问题），但是文字，border-radius等还是不能等比缩放的。

响应式布局
Rem
1rem即等于html元素的font-size值，根据屏幕大小动态地设置font-size的大小。rem可以实现字体等的等比缩放。 缺点：

对雪碧图不友好
不够精准
PC端兼容不好
本来16px的字显示已经够大，但由于使用了rem，在屏幕变大的同时，字体变大或导致翻页
其他
<meta name="apple-mobile-web-app-capable" content="yes">
苹果公司私有的meta标签，可以使web应用在全屏模式下运行，否则将用safari浏览器展示其内容

<meta name="format-detection" content="telephone=no">
默认情况下，Safari和IOS会自动识别像手机号码的文本，这个meta标签是用来禁止这项功能的

<link rel="apple-touch-icon" href="touch-icon-iphone.png">
<link rel="apple-touch-icon" sizes="76x76" href="touch-icon-ipad.png">
<link rel="apple-touch-icon" sizes="120x120" href="touch-icon-iphone-retina.png">
<link rel="apple-touch-icon" sizes="152x152" href="touch-icon-ipad-retina.png">

<link rel="apple-touch-startup-image" href="/startup.png">

<a href="tel:1-408-555-5555">Call me</a>
当用户将web应用添加到主屏幕时，这时候IOS可以提供配置应用图标的功能和启动图片的功能。还有类似连接其他native应用的功能。 相似地，Google Chrome在安卓平台上也提供了类似的功能，不同的是android会自动识别邮箱地址，而ios是电话号码：

<meta name="mobile-web-app-capable" content="yes">

<link rel="shortcut icon" sizes="196x196" href="icon-196x196.png">

<meta name="format-detection" content="email=no">
(Todos) 
