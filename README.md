# NodeJs-insert-picture
## 使用说明
-------------
1.下载代码，npm install<br />
2.node index.js即可<br />
## 博文
我们来分析下爬虫工作所需要的一些模块。<br />
首先，我们需要发送请求获得页面，在这里呢，我们用到了request-promise模块。<br />
<pre><code>
    const rp = require("request-promise"), //引入request-promise模块
      async getPage(URL) {  //这里用到es7中的"异步同步流"这个是我自己的说法，哈哈哈
        const data = {
          url, <br />
          res: await rp(
             url: URL
          }) 
      }; 
      return data //这样，我们返回了一个对象，就是这个页面的url和页面内容。
    }
</code></pre>
其次，用到一个模块为cheerio来解析我们上边得到的对象中res的字符串，cheerio的功能是将得到的res转为能像JQ操作DOM节点一样，方便我们操作<br />
<pre><code>
    const cheerio = require("cheerio");//引入Cheerio模块
    const $ = cheerio.load(data.res); //将html字符串模板转换为可操作的节点
</code></pre>
    
此时，我们要对我们即将进行爬取的页面进行分析。[www.mzitu.com/125685](www.mzitu.com/125685)，这是我们进行爬取的网址，F12查看DOM结构，根据这个结构我们可以使用$(".main-image").find("img")[0].attribs.src来爬取这张图片的地址（如果不知道为什么是attribs.src的话可以一步一步console.log()一下看看）。<br />
最后，到了最关键的时候，我们使用fs模块进行创建文件夹以及下载文件。这里用到了fs模块的几个指令：<br />
　　　　1.fs.mkdirSync(downloadPath)：查看是否存在这个文件夹。<br />
　　　　2.fs.mkdirSync(downloadPath)：创建文件夹。<br />
　　　　3.fs.createWriteStream(`${downloadPath}/${index}.jpg`):写入文件，这里需要注意的是fs.createWriteStream 似乎不会自己创建不存在的文件夹，所以在使用之前需要注意，保存文件的文件夹一定要提前创建。<br />
好的，大体的方法就是以上的几个模块和步骤。<br />
在这里，我针对这个网站的一些情况进行一下分析：<br />
        1.这个网站一个页面只有一张图片，但是每个页面的网址都是有根据的。[http://www.mzitu.com/125685](http://www.mzitu.com/125685)（当你输入[http://www.mzitu.com/125685/1](http://www.mzitu.com/125685/1)时也会跳转此页面），[http://www.mzitu.com/125685/2](http://www.mzitu.com/125685/2)等等。那么我们可以根据这个规律去爬取，并且我们需要在页面的下方的页码栏中获得这一组图图片的页码：<br />
        2.我们一般不会只爬取一组图片，但是这个网站的图片的标题也就是最后的六位数基本没有规律可言，那么我们只能从最开始的首页入手。具体方法不多做描述，与获取图片的URL方式相同。<br />
        3.同理，我们爬取完一页的目录之后会进行对第二个目录的爬取，[http://www.mzitu.com/page/2](http://www.mzitu.com/page/2/)，其原理和第一条相同。<br />
        4.但是，有的网站存在防盗链的情况，面对这种措施，我们需要伪造一个请求头来避开这个情况。这个可以从F12的Network中查到，看到这里的朋友我想也会明白。<br/>
<pre><code>
    let headers = {
          Accept: "text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8",
          "Accept-Encoding": "gzip, deflate",
          "Accept-Language": "zh-CN,zh;q=0.9,en;q=0.8",
          "Cache-Control": "no-cache",
          Host: "i.meizitu.net",
          Pragma: "no-cache",
          "Proxy-Connection": "keep-alive",
          Referer: data.url,//根据爬取的网址跟换
          "Upgrade-Insecure-Requests": 1,
          "User-Agent": "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.19 Safari/537.36"
    };
</code></pre>


总结：

郑重提升，爬虫虽好，一定不能触犯法律。

如果本本文触犯您的利益，请留言。

如果觉得本文不错，不要吝啬您的点赞和关注。谢谢。
