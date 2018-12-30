# WebSocket

> web 端的 Socket,Client 和 Server 建立 TCP 长连接，然后相互发送消息。

#### 浏览器发送 WebSocket 连接

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>WebSocket第一个案例</title>
  </head>
  <body>
    <h1>Echo Test</h1>
    <input type="text" id="sendTxt" /> <button id="sendBtn">发送</button>
    <div id="recv"></div>

    <script>
      var websocket = new WebSocket('wss://echo.websocket.org/');
      websocket.onopen = function() {
        console.log('websocket open');
        document.getElementById('recv').innerHTML = 'Connected';
      };
      websocket.onclose = function() {
        console.log('websocket close');
      };
      websocket.onmessage = function(e) {
        console.log(e.data);
        document.getElementById('recv').innerHTML = e.data;
      };
      document.getElementById('sendBtn').onclick = function() {
        var txt = document.getElementById('sendTxt').value;
        websocket.send(txt);
      };
    </script>
  </body>
</html>
```

#### nodejs 搭建 WebSocket Server

```
var ws = require('nodejs-websocket');

var PORT = 8001;

// Scream server example: "hi" -> "HI!!!"
var server = ws
  .createServer(function(conn) {
    console.log('New connection');
    conn.on('text', function(str) {
      console.log('Received ' + str);
      conn.sendText(str.toUpperCase() + '!!!');
    });
    conn.on('close', function(code, reason) {
      console.log('Connection closed');
    });
    conn.on('error', function(err) {
      console.log('handle err');
      console.log(err);
    });
  })
  .listen(PORT);

console.log('websocket server listening on port' + PORT);
```

#### socket.io

> socket.io 是一个跨浏览器支持 WebSocket 的实时通讯的 JS。

socket.io 的优势：

1. 在发送数据的时候，可以直接发送 JavaScript 对象；
2. 可以方便自定义消息类型。

`socket.io`实现简易网页聊天室例子：

`html:`

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>使用socket.io改进聊天室</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/socket.io/2.0.1/socket.io.js"></script>
  </head>
  <body>
    <h1>Chat Room</h1>
    <input type="text" id="sendTxt" /> <button id="sendBtn">发送</button>

    <script>
      var socket = io('ws://localhost:6001/');
      function showMessage(str, type) {
        var div = document.createElement('div');
        div.innerHTML = str;
        if (type == 'enter') {
          div.style.color = 'cornflowerblue';
        } else if (type == 'leave') {
          div.style.color = 'rosybrown';
        }
        document.body.appendChild(div);
      }
      document.getElementById('sendBtn').onclick = function() {
        var txt = document.getElementById('sendTxt').value;
        if (txt) {
          socket.emit('message', txt);
        }
      };
      socket.on('enter', function(data) {
        showMessage(data, 'enter');
      });
      socket.on('message', function(data) {
        showMessage(data, 'message');
      });
      socket.on('leave', function(data) {
        showMessage(data, 'leave');
      });
    </script>
  </body>
</html>
```

`Server:`

```
var app = require('http').createServer();
var io = require('socket.io')(app);

var PORT = 6001;

var clientCount = 0;

app.listen(PORT);

io.on('connection', function(socket) {
  clientCount++;
  socket.nickname = 'user' + clientCount;
  io.emit('enter', socket.nickname + ' come in');

  socket.on('message', function(str) {
    io.emit('message', socket.nickname + ' says:' + str);
  });
  socket.on('disconnect', function() {
    io.emit('leave', socket.nickname + ' left');
  });
});

console.log('websocket server listening on port' + PORT);

```
