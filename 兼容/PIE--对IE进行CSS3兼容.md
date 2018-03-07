# PIE
## 介绍
> 主角：PIE.js ,  PIE.htc    两种方法可以实现

> 官网下载：http://css3pie.com/download-latest
> 
> 官方网站：http://css3pie.com/
> 
> 演示地址：http://css3pie.com/demos/gradient-patterns/
>
>百度网盘：http://pan.baidu.com/s/1gfmyOzL
 
> 重要功能实现：
> 
> 可以使 IE6、7、8 、9 实现类似 chrome 和 firefox
> 
> 1. 更自然逼真的阴影效果，不再是以前那种丑陋的滤镜效果。
> 2. 实现很自然的圆角效果
> 3. 实现部分 CSS3  的强悍效果，如 多背景图，border-image，更强的背景渐变效果。
> 4. png 图片透明效果
## 用法
### 1 .htc 大概是浏览器补丁模式

```
/* 一 */
    <!-- [if lte IE 8]>
    <style type="text/css">
    .log-content{
        box-shadow: 0px 0px 5px  #888888;
        behavior: url(plugins/PIE.htc);
    }
    </style>
    <![endif] -->
/* 二 */
    .log-content{
        box-shadow: 0px 0px 5px  #888888;
        behavior: url(plugins/PIE.htc);
    }
```
- 最好使用一方法写在html页面<head></head>中，不然经常时好时坏
- 注意路径，最好是直接使用 URL 的绝对路径
- 如果无法显示可能是服务器端不支持该类型文件格式的解析，可以在服务器配置文件的mime.types文件的末尾增加一行：text/x-component htc，具体方式请自己 google

###  2  .js  方式，很灵活，简单
> 使用  PIE.js  文件

```
<script type="text/javascript" src="PIE.js"></script> 
<script type="text/javascript"> 
PIE.attach(document.getElementById("test")); 
</script>
```
最好放在 body 的最下面，这样将其包含到 html 页面后既可以使用

另外推荐配合 jQuery 使用更方便


```
<script type="text/javascript" src="http://code.jquery.com/jquery-1.7.2.min.js"></script>
<script type="text/javascript" src="/PIE.js"></script>
<script type="text/javascript">
(function($){
    $.pie = function(name, v){
        // 如果没有加载 PIE 则直接终止
        if (! PIE) return false;
        // 是否 jQuery 对象或者选择器名称
        var obj = typeof name == 'object' ? name : $(name);
        // 指定运行插件的 IE 浏览器版本
        var version = 9;
        // 未指定则默认使用 ie10 以下全兼容模式
        if (typeof v != 'number' && v < 9) {
            version = v;
        }
        // 可对指定的多个 jQuery 对象进行样式兼容
        if ($.browser.msie && obj.size() > 0) {
            if ($.browser.version*1 <= version*1) {
                obj.each(function(){
                    PIE.attach(this);
                });
            }
        }
    }
})(jQuery);
 
$(function(){
    $.pie('.for-ie6', 6);
 
    var objs = $('.for-ie, .test, .test1, .test2 .test3, #test4');
    $.pie(objs, 9);
});
</script>
```



## 透明的方法：
> 直接对执行过PIE兼容的当前容器进行透明度是没有用的，因为JS会新生成一个自定义标签层叠在下面，可以用下面的步骤实现透明
> 1.  CSS 添加  
```
css3-container { filter:alpha(opacity=98); }
```
> 2.  JS 添加
```
$('.test').prev('css3-container').css({
'filter':'alpha(opacity=98)', 'opacity':0.98});
```

## 附加经验
> 1.  使用圆角，阴影时，最好给其定义下样式  position:relative; 否则可能遇到莫名其妙的层高问题导致生成的兼容样式层看不到了；
> 
> 2.  使用png透明图片做背景时，如果没有其它圆角、阴影等样式，IE6下貌似是不会自动实现透明的，可以试试 -pie-background:url(); 的方式 或者添加一个  border-radius:0.5px; 样式既可以实现背景透明
> 
> 3.  IE6 下 使用背景图片透明时，尽量不要是用 css sprtes 方法，将很多图片放一张图，否则在兼容渲染的时候效率会非常差，因为图片太大啦，尽量 IE6 使用单独的png图片进行透明渲染
> 
> 4.  使用阴影时，如果有设置渐变透明可能会导致一片黑，目前解决办法可以试试 使用半透明的 png 图片做背景试试，这个我还没测试过，调整 opacity 透明度 滤镜貌似也不成功，算是完美中的不足吧。
> 
> 5.  应该是不支持内阴影效果的，没弄出来过，好遗憾


参考： http://www.zhangxinxu.com/wordpress/?p=967

参考：http://blog.zzstudio.net/web/article_249.html