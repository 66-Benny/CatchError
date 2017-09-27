# 错误监控

### 无论是问怎么监测 JS 的代码错误还是如何保证产品的质量，都是在问错误的监控。

### 前端错误的分类
> - 及时运行错误：代码错误
> - 资源加载错误：图片、JS、CSS加载失败

### 错误的捕获方式
> 及时运行错误的捕获方式
> - 1. try...catch
> - 2. window.onerror

> 资源加载错误
> - 1.object.onerror
> - 2.performance.getEntries() (下面将详细说下该方法的使用)
> - 3.Error事件捕获

**performance.getEntries() 返回的是一个数组**
performance.getEntries().forEach(item => console.log(item)) 将打印出**所有成功加载的资源**，比方说我们要看页面上所有 image是否都加载成功了。可以用 document.getElementsByTagName('img') 获取所有 image，**用获取到的所有 image 减去加载成功的 image，剩下的就是未加载成功的**。

```
(function () {
    // 浏览器不支持，就算了！
    if (!window.performance && !window.performance.getEntries) {
        return false;
    }
    var result = [];
    // 获取当前页面所有请求对应的PerformanceResourceTiming对象进行分析
    window.performance.getEntries().forEach(perf => {
        if (perf.initiatorType === 'img') {
            result.push({
                'url': perf.name,
                'entryType': perf.entryType,
                'type': perf.initiatorType,
                'duration(ms)': perf.duration
            });
        }
    });
    // 控制台输出统计结果
    console.table(result);
})();
```
**Error事件捕获**
```
// true 捕获阶段
window.addEventListener('error', e => console.log('Error事件捕获：', e), true)
```

**延伸：跨域的 JS 可以捕获到吗？**
可以捕获到，有以下两种方法：
> - 1. 在 script 标签增加  crossorign 属性
> - 2. 设置 JS 资源响应头 Access-Control-Allow-Origin:*

### 上报错误的基本方式
> - 1. 采用 Ajax 通信的方式上报
> - 2. 利用 Image 对象上报
```
(new Image()).src = 'http://www.example.com/getReport.php'
```