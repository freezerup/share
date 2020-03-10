## 一、概述
众所周知，JavaScript语言采用的是单线程模型，所有任务只能在一个线程上完成，一次只能做一件事，而Web Wokers实现了线程在后台运行，即多线程运行
## 二、基本用法
### 2.1 主线程
#### 2.1.1 新建一个 Worker 线程
``` javascript
var worker = new Wokers('work.js');  
```

调用Worker() 的构造器，参数为一个脚本文件，子线程在此脚本中执行。
注：脚本文件必须与主线程的脚本文件同源，不能是本地文件。
#### 2.1.2 消息的发送
```javascript
worker.postMessage({name: 'TimeCountDown', query: {num: 60}});  
```
worker.postMessage()的参数，即使发送到子线程的数据，它可以是各种数据类型，包括二进制数据。
#### 2.1.3 消息的接收
```javascript 
// 写法一：
worker.onmessage = function (e) {
  console.log(e.data);
};
// 写法二：
worker.addEventListener('message', function (e) {
 console.log(e.data);
}, false); 
```
写法下同。
#### 2.1.4 结束子线程
```javascript
worker.terminate();
```
#### 2.1.5 错误处理
```javascript
worker.onerror = function (e) {
	console.log([
		'ERROR: Line ', e.lineno, ' in ', e.filename, ': ', e.message  
	].join(''));  
});
```
### 2.2 worker线程
#### 2.2.1 监听message事件
```javascript
self.onmessage = (event) => {  
	self.postMessage(e.data);   
} 
```
self代表子线程自身，即子线程的全局对象，可以不写，也可以使用this代替。
#### 2.2.2 发送消息
```javascript
self.postMessage(e.data);
```
self 同上。
#### 2.2.3 加载脚本
```javascript
importScripts('axios.js');
```
importScripts属于Worker内部方法。
该方法可以同时加载多个脚本
```
importScripts('axios.js', 'script.js');
```
#### 2.2.4 关闭Worker
```javascript
self.close();
```
## 三、实例:同页面的 Web Worker
 
```javascript
<div id="box">60</div>  
<div id="box2"></div> 
<script id="worker" type="app/worker" name="myWorker">
   var workerFunctions = {  
     TimeCountDown: function(query) {  
        var num = query.num;   
        var T = setInterval(() => {  
          this.postMessage(--num);   
          if(num <= 0){   
            clearInterval(T);   
            this.close();   
          }   
        }, 1000);  
      },  
      clickEvent: function(query) {  
        var num = query.num;   
        var T = setInterval(() => {   
          this.postMessage(--num);   
        }, 1000);  
      }  
    }  

    self.onmessage = (event) => {   
      var data = JSON.parse(event.data);   
      var funName = data.name;  
      var query = data.query;  
      workerFunctions[funName].call(self, query);
    }
  </script>
  <script>
    var box = document.getElementById('box');  
    //判断浏览器是否支持  
    if (typeof (Worker) === "function") {  
      var blob = new Blob([document.querySelector('#worker').textContent]);  
      var url = window.URL.createObjectURL(blob);  
      var worker = new Worker(url, { name: 'myWorker'});  
      
      worker.postMessage(JSON.stringify({name: 'TimeCountDown', query: {num: 60}}));  
      worker.onmessage = function (e) {  
        box.innerText = e.data;  
      };  

      window.addEventListener('click', function(event) {  
        const value = event.target.attributes['data-id'].value;   
        var worker2 = new Worker(url);  

        worker2.postMessage(JSON.stringify({name: 'clickEvent', query: {num: 60}}));  
        worker2.onmessage = function (e) {  
          box2.innerText = e.data;  
          if (e.data === 30) {  
            worker2.terminate();  
          }  
        };  
      }, false);  
    }
  </script>
```
## 四、[*兼容性*](https://caniuse.com/#search=web%20workers)

## 五、调试
目前chrome浏览器的debug工具已经支持调试Web Worker线程中的代码了。
具体操作方法，就是在script对应的tab页的右下角勾选Worker的调试选项，如图所示：
![](https://pic.wozaijia.com/upload/test/chorme-sources-worker.jpg)

 
&gt;学习文献：
&gt;* [*使用 Web Workers - Web API 接口 | MDN*](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Workers_API/Using_web_workers)
&gt;* [*Web Worker 使用教程--阮一峰*](http://www.ruanyifeng.com/blog/2018/07/web-worker.html)
