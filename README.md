# The HTTP Request

## Objectives

1. Define the path in an HTTP request
2. Define the `env` variable in an HTTP request
3. Use the `path` method to filter HTTP requests
4. Describe `GET` parameters and how they allow web applications to handle user queries

## HTTP Requests

An HTTP Request that our browser sends to the server contains two main sections of information. One is headers and the other section is the resource or path requested (for example, `/search` or `/profile_name`). Let's break down the second section since there's a lot of information that can be stored in it! 

## The Path

The path that is requested is the resource that the client wants. Since your server can contain a lot of functionality, the path signifies which specific part of your server it wants. If we were creating a simple shopping cart application, for example, we can think of a few different paths that are required:

| /items | List all items available |
|--------|--------------------------|
| /cart  | List items in cart       |

How would we implement this in our Rack app? The path lives in the HTTP request, and to get to it we have to inspect the `env` part of our `#call` function. In the `env` variable is all of the information contained in the request. Thankfully, Rack has a great way of parsing all this information for us. It looks like this:

```ruby
class Application

  def call(env)
    resp = Rack::Response.new
    req = Rack::Request.new(env)
    resp.finish
  end
  
end
```

This [Rack::Request](http://www.rubydoc.info/gems/rack/Rack/Request) instance now has a ton of useful methods. If we look through the documentation, it has a method called `#path`. This will return the path that was requested. 

Before we inspect the `#path`, let's set up what we would do if someone wanted to see all of our items:

```ruby
class Application

  @@items = ["Apples","Carrots","Pears"]

  def call(env)
    resp = Rack::Response.new
    req = Rack::Request.new(env)
    
    @@items.each do |item|
      resp.write "#{item}\n"
    end

    resp.finish
  end
```

Now we put all of your items out. The problem though is that this will list all of your items *no matter what path you put in*. Give it a try. Create your Rack file with the above code. Type `localhost:9292/items`, `localhost:9292/cart`, `localhost:9292/flatiron/is/awesome`. All of those URLs work since we're not filtering for path. In other words, no matter what the request is, we send the same response. Let's filter so that this only works for the `/items` path using the `#path` method of our `Rack::Request` object:


```ruby
class Application

  @@items = ["Apples","Carrots","Pears"]

  def call(env)
    resp = Rack::Response.new
    req = Rack::Request.new(env)

    if req.path.match(/items/)
      @@items.each do |item|
        resp.write "#{item}\n"
      end
    else
      resp.write "Path Not Found"
    end

    resp.finish
  end
```

Great! With this we can now do different things depending on the path.

### User Input Via The Path

What if users wanted to check and see if we have `Apples` available in our list of items? How do other websites handle getting user queries? If we go to GitHub and type in "apples" in the search query, we get a URL that looks like this: `https://github.com/search?q=apples`. We have a domain of `github.com`, path of `search`, and then a `?` character. After that character comes `q=apples`. There is our search! 

The section after the `?` is called the `GET` parameters. If you notice in the above example, `GET` params come in key/value pairs. The key in GitHub's case would be `q` and the value is `apples`. The matching Ruby data structure that is also a key/value store would be a `Hash`! Thankfully, Rack provides the mechanism to parse the `GET` params and return them to us in a standard `Hash`. If we wanted to implement a `/search` route that accepted a `GET` param with the key `q` it would look something like this:


```ruby
class Application

  @@items = ["Apples","Carrots","Pears"]

  def call(env)
    resp = Rack::Response.new
    req = Rack::Request.new(env)

    if req.path.match(/items/)
      @@items.each do |item|
        resp.write "#{item}\n"
      end
    elsif req.path.match(/search/)

      search_term = req.params["q"]

      if @@items.include?(search_term)
        resp.write "#{search_term} is one of our items"
      else
        resp.write "Couldn't find #{search_term}"
      end

    else
      resp.write "Path Not Found"
    end

    resp.finish
  end
```

<p data-visibility='hidden'>View <a href='https://learn.co/lessons/rack-http-request-readme' title='The HTTP Request'>The HTTP Request</a> on Learn.co and start learning to code for free.</p>
