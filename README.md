# 一种完美的Markdown转PDF方案

## 一、MarkDown工具排行
自从学了markdown，从此特别喜欢用markdown写东西，用过的工具有:

- vscode
- [简书](http://www.jianshu.com/)
- [Cmd Markdown](https://www.zybuluo.com/cmd/)
- [StackEdit](https://stackedit.io/)
- [DILLINGER](https://dillinger.io)
- [Markable.in](https://markable.in/)
- [MarkdownPad](http://markdownpad.com/)  

这些些Markdown的工具里首推前三个，vscode不用说为什么。简书是因为自动保存和版本控制很方便，同时是在线保存的，上传图片也是最方便的，不需要再去找图床，只要有浏览器就可以随时接着写。Cmd Markdown是兼具简书的优点，是增强型的markdown语法，支持流程图和甘特图等，还能导出成多种格式的文件。

## 二、导出成PDF
Markdown用的多了，有时候要给其他人写一些文档就很不适应再用Word啥的，但是又不能要求别人能够把md文件拖到解析markdown的工具里显示，于是导出成PDF就成了一种比较好的解决方案。于是问题来了？

> 如何方便的将markdown文件导出成漂亮的PDF文件？  

市面上大多数将markdown导出成pdf的工具都需要开会员，于是我找了两个方法来解决这个问题。

## 三、两种导出漂亮PDF文件的方法
### 1. 通过vscode插件
上面的集中书写工具中，vscode可以通过安装`Markdown PDf`插件，将markdown格式的文件导出成PDF，同时支持自定义markdown的样式，成了一种不错的解决方案，但是需要自己写css样式或者去网上找比较漂亮的样式，否则导出的PDF文件美观程度上实在差强人意。
> 在window系统下，通过上面的步骤就可以了，但是在Linux或者MAC下，安装后右键--convert to pdf会报错：ERROR: phantomjs binary does not exist: /Users/username/.vscode/extensions/yzane.markdown-pdf-0.1.7/node_modules/phantomjs-prebuilt/lib/phantom\bin\phantomjs.exe
这时候需要安装phantomjs才可以使用：
MAC：brew install phantomjs
Linux：sudo apt-apt install phantomjs

### 2. 通过Cmd Markdown导出的无样式html
第二种方法比较复杂，但是可以导出比较漂亮PDF文件，并且支持甘特图和流程图等，当然如果第一种方法有比较漂亮的CSS样式且不需要用太多复杂的加强markdown语法，就还是建议用第一种方案，毕竟更简单。
#### 第一步：将markdown转换为html文件
Cmd Markdown 支持导出无样式的html、有样式的html、PDF，但是后两者需要开通会员，不用去考虑。就选择导出为无样式的html，导出后得到的显示效果如下：

![不带样式的html](http://upload-images.jianshu.io/upload_images/2662764-90d3c5d69f8f73da.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 第二步：将html文件补足样式
这样得到的html文件没有样式并不好看，哪去哪找漂亮的样式呢？答案是[Cmd Markdown 自己的带样式网页](https://www.zybuluo.com/static/editor/cmd-manual.html)。
从这个页面上把css下载过来，link到不带样式的html文件中，得到带样式的网页：

![带样式的html](http://upload-images.jianshu.io/upload_images/2662764-5b4f301c31ff5cb3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

但是还存在一个问题，link了CSS文件后，内容的显示占了屏幕的100%宽度，并不好看 ，自己对比之后发现官方的页面中id为`wmd-preview`的元素的Class为`wmd-preview wmd-preview-full-reader`，而导出的无样式文件不含`wmd-preview-full-reader`这个class，可以用js插入这个class：
```javascript
<script src="https://cdn.bootcss.com/jquery/2.2.4/jquery.min.js"></script>
<script>
  $(document).ready(function () {
    // 修改样式
    $("#wmd-preview").addClass("wmd-preview-full-reader");
    })
  });
</script>
```

#### 第三步：将html页面进一步美化
Cmd Markdown的官方样式已经很漂亮了，但是比较简书，有一点美中不足，简书中插入的图片下面会有一个图片的alt作为图片的标题，可以让文档看起来更清晰，还有其他的一些细节也是比Cmd Markdown做的好的。因此结合此两者的样式就可以得到一个漂亮的样式了。

![简书下用alt作为图片的名字](http://upload-images.jianshu.io/upload_images/2662764-94a819ed95fad14a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1080/q/50)

因此可以进一步修改Cmd Markdown的官方样式，使其拥有简书的效果，代码如下：
Js修改样式：
```javascript
<link href="../static/assets/myCss.css" rel="stylesheet" media="screen">
<script src="https://cdn.bootcss.com/jquery/2.2.4/jquery.min.js"></script>
<script>
  $(document).ready(function () {
    // 修改样式
    $("#wmd-preview").addClass("wmd-preview-full-reader");
    $("body").css("background-color", "#fcfaf2");
    $(".white-blockquote").css("background", "whitesmoke");
    $("body").css("background-color", "#fcfaf2");
    $("h1,h2,h3,h4,h5,h6").css("font-weight", "700");
    $("pre.prettyprint").css("background-color", "whitesmoke");
    $(".theme-white").css("background-color", "#e5e5e5");
    $(".theme-white.sequence-diagram [fill='#ffffff'], .theme-white.flow-diagram [fill='#ffffff']").css("fill", "#e5e5e5");
    $("img").parent().addClass("image-package")
    $("img").each(function () {
      var that = $(this);
      var alt = that.attr("alt");
      var add = '<br><div class="image-caption">'+ alt +'</div>';
      var html = that.parent().html() + add;
      that.parent().html(html);
    })
  });
</script>
```
复制简书该图片效果的样式到myCss.css中
```css
.image-package {
  padding-bottom: 25px;
  text-align: center;
}

.image-caption {
  min-width: 20%;
  max-width: 80%;
  min-height: 22px;
  display: inline-block;
  padding: 10px;
  margin: 0 auto;
  border-bottom: 1px solid #d9d9d9;
  font-size: 14px;
  color: #969696;
  line-height: 1.7;
}
```
修改后得到的html显示效果如下：

![image.png](http://upload-images.jianshu.io/upload_images/2662764-8ad47d0bbb9b4c81.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

自此，就成功的将markdown转变成了比较漂亮的html文件。[样例地址](http://md.yfree.cc/page)
这时候可以把文件上传到gitpage或者自己的服务器，然后得到链接就能发给其他人看了，但是一般大家也不会用html文件来作为互相发送的文档（虽然我个人觉得这样用起来也可以），所以继续下一步：将html转换为pdf。

#### 第四步：将html转换为pdf
在网上找了很久，将html转换为html大多也是要付费的，但也有免费而且效果不错的，下面列举出来：
1. 用chrome浏览器，Ctrl+P，然后点击*更改*，可以选择*保存为PDF*，但是这样导出的PDF没有样式，不推荐，不过也在此提一下；
2. [PDFCROWD](https://pdfcrowd.com/)，这个可以导出带样式的，但是需要先将html文件带上样式都传到服务器上，然后把链接给过去，缺点是不付费的话导出的pdf纸张大小很奇怪，还每一页都加上了水印，也不推荐。
3. [EXPERT](https://www.html-to-pdf.net/free-online-pdf-converter.aspx)，这个可以完美的导出带样式的PDF，但是要求先把html文件和样式传到服务器上，然后给出链接。推荐给有服务器的同学。
4. [wkhtmltopdf](https://wkhtmltopdf.org/index.html)，最推荐的一个，本地安装一下，然后敲个命令行就可以了。
```shell
wkhtmltopdf 本地或网上页面的地址 要导出的路径
```
## 四、github项目地址
[自修改的样式和示例文件]()