A Flask extension for HTML5 server-sent events support, powered by Redis.

**使用条件**：

需要配置Redis；

不要使用flask内置的开发服务器，要用异步服务器，如Gunicorn

---

```python
from flask import Flask, render_template
from flask_sse import sse

app = Flask(__name__)
app.config["REDIS_URL"] = "redis://localhost"  # 要配置REDIS
# app.config["REDIS_URL"] = "redis://:password@localhost"
app.register_blueprint(sse, url_prefix='/stream')

@app.route('/')
def index():
    return render_template("index.html")

@app.route('/hello')
def publish_hello():
    sse.publish({"message": "Hello!"}, type='greeting')
    '''
    通过指定chanel参数，可以把消息发到订阅该channel的那些接受者
    channel参数名字可以是任意字符串，且时动态创建的。
    sse.publish({"user": "alice", "status": "message to ch1"}, channel="ch1")
    sse.publish({"user": "alice", "status": "message to ch2"}, channel="ch2")
    '''
    return "Message sent!"
```

```
订阅频道
1. if your event stream is at /stream, you can connect to the “users.social” channel by using the URL /stream?channel=users.social. 
		 js代码：  var source = new EventSource("/stream?channel=users.social");
2. use Flask’s url_for() function to generate this query parameter, like so:
			 var source = new EventSource("url_for('sse.stream', channel='users.social')")


```

```html
<!DOCTYPE html>
<html>
<head>
  <title>Flask-SSE Quickstart</title>
</head>
<body>
  <h1>Flask-SSE Quickstart</h1>
  <script>
    var source = new EventSource("{{ url_for('sse.stream') }}");
    source.addEventListener('greeting', function(event) {
        var data = JSON.parse(event.data);
        alert("The server says " + data.message);
    }, false);
    source.addEventListener('error', function(event) {
        alert("Failed to connect to event stream. Is Redis running?");
    }, false);
  </script>
</body>
</html>
```

