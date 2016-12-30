---
title: Chrome扩展开发实践——外部数据的获取和存储
date: 2016-07-30 13:25:43
tags: [Chrome,JavaScript]
categories: Tech
description: 最近尝试着写一个Chrome的天气扩展。几番折腾下来，遇到了不少问题。
---

## 前言

最近尝试着写一个Chrome的天气扩展。几番折腾下来，遇到了不少问题。

## 正文

关于Chrome扩展的开发，当然是要看Google的Chrome开发者文档了：[Chrome文档](https://developer.chrome.com/extensions/getstarted); 
这个是全英文的，然而360很良心地把开发者文档翻译了一遍：[360文档](http://open.chrome.360.cn/extension_dev/overview.html);

### 关于API和JSON处理

我的想法是这样的，这个浏览器扩展能够得到设备的地理位置并返回当前位置的天气状况，而JavaScript没有API来得到地理位置信息，必须得先获取ip并通过第三方ip地址查询接口来得到所在城市的信息。(我用的是新浪的接口)

```
http://int.dpool.sina.com.cn/iplookup/iplookup.php?format=json  

```

其内容如下：

```
{"ret":1,"start":-1,"end":-1,"country":"\u4e2d\u56fd","province":"\u6d59\u6c5f","city":"\u5b81\u6ce2","district":"","isp":"","type":"","desc":""};
```

可以看到这里国家、省、市的值都是Unicode编码，然而把值取出来时`data.city`却自动变为了中文，非常之神奇...

关于天气API，我用的是和风天气，免费版一天能使用3000次。把城市名作为参数，通过ajax得到的JSON数据是这样的:

```json
{
    "HeWeather data service 3.0": [{
        "aqi": {
            "city": {
                "aqi": "121",
                "co": "1",
                "no2": "22",
                "o3": "143",
                "pm10": "59",
                "pm25": "38",
                "qlty": "轻度污染",
                "so2": "7"
            }
        },
        "basic": {
            "city": "宁波",
            "cnty": "中国",
            "id": "CN101210401",
            "lat": "29.872000",
            "lon": "121.542000",
            "update": {
                "loc": "2016-07-27 17:52",
                "utc": "2016-07-27 09:52"
            }
        },
        ......
    }]
}
```

设`data`为JSON对象，那么`data["HeWeather data service 3.0"][0].aqi.city.qlty`就是空气质量的描述，值为`轻度污染`。

关于JSON对象的操作： 

1. [JSON](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/JSON) 
2. [JSON.stringify()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 
3. [JSON.parse()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse)

### Chrome Content Security Policy问题

当然这里还有一个大坑，那就是

> Chrome遵循Content Security Policy (CSP)的理念，引入了严格的策略使扩展更安全，同时提供创建和实施策略规则的能力，这些规则用以控制扩展（或应用）能够加载的资源和执行的脚本。

也就是说，代码无法引用外部资源，无法使用CDN上的js和css，新浪api也无法以script标签形式存在。 
查了一下[文档](https://developer.chrome.com/extensions/contentSecurityPolicy#relaxing),发下以下代码和外部引用不被允许： 

1. eval()函数 
2. html内嵌的js代码 
3. 网上的js等资源

当然，还有个trick可以引用外部的JavaScript或者资源

> 放宽默认策略
> 没有绕过“禁止Inline JavaScript执行”的办法。即使有意在脚本策略字段增加unsafe-inline也是没有任何作用的。就是这样设计的！ 不过，如果您需要一些外部的JavaScript或者资源，您可以通过将HTTPS源的脚本加入白名单来放宽“只加载本地脚本和资源”策略。 请记住：白名单对于不安全的HTTP源是无效的。这也是特意设计的！因为必须确保可执行资源的加载正是您所期望的，而不是被网络攻击者替换过的。 而中间人在HTTP上已经是小菜一碟，且还很难检测到，因此只支持HTTPS源。 例如，允许加载来源自HTTS的example.com网站的脚本，策略定义如下： { ..., "content*security*policy": "script-src 'self' [https://example.com](https://example.com/); object-src 'self'", ... } 请注意，上面的策略中，script-src和object-src都定义了。Chrome要求：必须定义这两者！如果您不知道怎么定义，就让它们都是'self'。 通常，可以通过Google Analytics的统计结果来帮助定义策略。在brief tutorial 我们提供各种各样的扩展分析样板，和一个更详细的简短教程。

虽然有这个trick，但能不能用还是要看运气。亲测新浪api不可以用，一些js文件还是要下载到本地才能使用。继续翻文档，发现manifest.json中的[permissions](https://developer.chrome.com/extensions/declare_permissions)就是给调用API设计的。

```json
{
  ...
  "permissions": [
    "activeTab",
    "https://ajax.googleapis.com/",
    "http://int.dpool.sina.com.cn/iplookup/iplookup.php?format=json"
  ]
  ...
}
```

### 缓存数据问题

由于和风天气一天只能访问3000次，如果能把数据保存一段时间就好了。之前想过用cookie存储数据，后来看到了HTML5的数据存储方案：[HTML5 Web存储](http://www.w3school.com.cn/html5/html_5_webstorage.asp), 里面介绍到

> cookie 不适合大量数据的存储，因为它们由每个对服务器的请求来传递，这使得 cookie 速度很慢而且效率也不高。 在 HTML5 中，数据不是由每个服务器请求传递的，而是只有在请求时使用数据。它使在不影响网站性能的情况下存储大量数据成为可能。

HTML5提供了两种在客户端存储数据的方法： 
\1. localStorage - 没有时间限制的数据存储 
\2. sessionStorage - 针对一个 session 的数据存储

使用HTML5这种方案，代码也十分简单(通过键值对`key-value`存储,将存储对象转换为字符串后存入)：

```javascript
localStorage.nickname = "Mike";  
console.log(localStorage.nickname); // Mike

localStorage.setItem("age","18"); // 设置b为"isaac"  
localStorage.getTime("age"); //18

var a = localStorage.key(0); // 获取第0个数据项的键名，此处即为“nickname”

localStorage.removeItem("age");//清除age的值

localStorage.clear();//清除当前域名下的所有localstorage数据  
```

而用`sessionStorage`存储的数据，当用于关闭浏览器窗口的时候，数据也会被删除。

利用`localStorage`缓存天气数据(时限1小时)：

```javascript
var storedTime = localStorage.updateTime||0;  
var time = new Date().getTime() - (60 * 60 * 100); //每小时请求一次数据  
httpRequest('http://int.dpool.sina.com.cn/iplookup/iplookup.php?format=json', function(data) {  
  if (!data) {
    return;
  }
  data = JSON.parse(data);
  var cityName = data.city;
  if (time > storedTime) {
    httpRequest("https://api.heweather.com/x3/weather?city=" + cityName + "&key=yourkey", function(weatherData) {
      weatherData = JSON.parse(weatherData)
      showData(cityName, weatherData);
      localStorage.updateTime = new Date().getTime();
      localStorage.data = JSON.stringify(weatherData);
    });
  } else {
      weatherData = JSON.parse(localStorage.data);
      showData(cityName, weatherData);
  }
});

function showData(city, data) {  
    //Show weather data by dealing with DOM.
}
```

解决了这个问题之后，接下来就是画网页了，我的扩展功能十分简单，只是显示当天的天气情况= =。![img](http://o6ljw8wcq.bkt.clouddn.com/myblog/jpg/QQ%E5%9B%BE%E7%89%8720160729155721.png)

一个非常简易的Chrome扩展就写完了。

[localStorage参考资料](https://segmentfault.com/a/1190000004121465)

[天气预报扩展 源代码地址](https://github.com/clarkzsd/TinyWeather)