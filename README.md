# HTTP Request

### What is a HTTP request?

A request is the initial communication that opens up a communication channel between the client and the server. In HTTP, this relationship is called the `HTTP Client-Server model`. The *request* sent by the client will send the parameters of the information that the client is requesting, and then the server will send back a *response* that contains the requested information.

### Structure of a HTTP request

A HTTP request consists of several key pieces of information: an initial request line that contains the parameters of the request being made, one or several headers that contain relevant metadata, and then finally an optional message body that conveys more information about the request.

Let's see the example below:

```
GET /path/to/file/index.html HTTP/1.0
From: ian@flatironschool.com
User-Agent: Mozilla/3.0Gold
```

What's happening here? So we are making a `GET` request to a URL, which in this case is `/path/to/file/index.html`, and the `HTTP/1.0` parameter is the version of the HTTP protocol we're using. The request is being made from `ian@flatironschool.com`. The `User-Agent: Mozilla/3.0Gold` line is a metadata specification that specifies what software agent our request is using. In this case, `Mozilla/3.0Gold` is a software agent that does the heavy lifting of making the request.

This request will prompt a response back from the server, which we will cover next.
