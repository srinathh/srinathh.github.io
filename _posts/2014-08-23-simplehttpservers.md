---
layout: post
title: "Hello World Web Servers in Python & Go"
tags: programming coding go python golang
permalink: /blog/simplehttpservers/
author: "Hariharan Srinath"
---

Hello World programs are the first programs typically introduced in any
programming language tutorial. However, in our modern era of web applications
they are not particularly insightful into the strenghts of a language to support
web development. In this article, we compare Hello World web servers written in 
Python & Go languages.

## Go Language
The [Go Language](http://golang.org/) released by Google is notable for its terse C Language inspired 
syntax (indeed [Ken Thompson](http://en.wikipedia.org/wiki/Ken_Thompson is one of the inventors) 
that nevertheless removes a lot of boilerplate. It has a unique approach to concurrency 
and derives high performance from being a compiled language. 

Go comes with extensive support for developing web applications within its
standard library including a robust templating solution. For serving simple static 
or dynamic driven websites, onerarely need anything more than this. 
For fancier applications, third party libraries provide more complex routers, 
context packages etc. The [Hello World](/code/helloworld.go) web server is 
effectively just 3 lines of code and can support many concurrent connections.

```go

package main

import (
    "fmt"
    "log"
    "net/http"
)

func main() {
    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        fmt.Fprintf(w, "Hello World")
    })
    log.Fatal(http.ListenAndServe(":8080", nil))
}

```

## Python
[Python](https://www.python.org/) is among the simplest of modern programming languages to learn due to its guiding
philosophy of [_There should be one and preferably only one obvious way to do it_](http://legacy.python.org/dev/peps/pep-0020/).
Python's ease and a large base of mature third party libraries has made it a language
of choice among startups looking to iterate their minimum viable product quickly accepting
the performance penalty of an interpreted language.

Python's standard library has been designed for a prior era and it is definitely 
not as simple to spin up a [Hello World](/code/helloworld.py) webserver as in Go. 

```python

import BaseHTTPServer

HOST_NAME = 'localhost'
PORT_NUMBER = 8080

class MyHandler(BaseHTTPServer.BaseHTTPRequestHandler):
    def do_GET(s):
        s.send_response(200)
        s.send_header("Content-type", "text/text")
        s.end_headers()
        s.wfile.write("Hello World")

if __name__ == '__main__':
    server_class = BaseHTTPServer.HTTPServer
    httpd = server_class((HOST_NAME, PORT_NUMBER), MyHandler)
    try:
        httpd.serve_forever()
    except KeyboardInterrupt:
        pass
    httpd.server_close()

```

However modern web development with Python typically relies on one of the 
many third party web frameworks, notably [Django](https://www.djangoproject.com/), to speed up web development. 
As an example, look at the simplification coming from the popular [Flask](http://flask.pocoo.org/) micro-framework.
However, Python's lack of in-built support for easy concurrency means that often, 
jury-rigged solutions need to be employed in production to scale

```python
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello World!"

if __name__ == "__main__":
    app.run()
```