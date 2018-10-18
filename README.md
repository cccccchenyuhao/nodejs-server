# 搭建nodejs服务器
1. 路由设计
  3个页面 
   - "/index”
   - "/weather" 一个表单页面，输入城市使用AJAX获取数据，数据存在"/data/weather.json"
   - "/register” 使用AJAX以POST发送数据到“/register”
   
  2个接口
   - "/getweather" GET 请求天气数据 
   - "/rigister" POST 发送注册数据

  开放静态文件

   - “/static”

2. 代码分析

将路由封装成如下形式的对象

```javascript
var getRoutes = {
    "/index": function (req, res) {
        fs.readFile(path.join(__dirname, 'views/index.html'), function (err, data) {
            if (err) {
                console.log("404 NOT FOUND")
            }
            res.end(data.toString())
        })
    }
}    
```



路由处理函数

```javascript
function routesHandle(req, res) {
    var pathname = url.parse(req.url).pathname
    if (pathname === "/") {
        pathname += "index"
    }
    if (getRoutes[pathname] && req.method === "GET") {
        getRoutes[pathname](req, res)
    } else if (postRoutes[pathname] && req.method === "POST") {
        postRoutes[pathname](req, res)
    }else {
        staticRoot(req, res)
    }
}
```



静态文件处理

```javascript
function staticRoot(req, res) {
    fs.readFile(path.join(__dirname, "static", req.url),function (err, data) {
        if (err) {
            res.writeHead('404', 'Not Found')
            res.end()
        } else{
            res.end(data)
        }       
    })
}
```



POST请求体处理

```javascript
function parseBody(body){
  console.log(body)
  var obj = {}
  body.split('&').forEach(function(str){
    obj[str.split('=')[0]] = str.split('=')[1]
  })
  return obj
}
  var body = ''
  req.on('data', function(chunk){
    body += chunk
  }).on('end', function(){
    req.body = parseBody(body)
  })
```



创建服务器监听3000端口

```javascript
http.createServer(function (req, res) {
    routesHandle(req, res)
}).listen(3000, function () {
    console.log('running')
})
```

