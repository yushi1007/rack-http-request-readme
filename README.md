# The HTTP Request

We've talked a bit about creating the HTTP Response, let's discuss what is contained in the HTTP Request that our browser sends to the server. The request contains two main sections of information: The resource or path requested (`/search` or `/profile_name`) and it contains headers. Let's break down those two sections because a lot of data can be stored in each sections!

## The Path

You've seen it all over the place. The path that is requested is the resource that the client wants. Of all the functionality your server contains. The path signifies which specific part of your server it wants. If we were creating a simple shopping cart application, we can think of a few different paths that are required:

| /items | List all items available |
|--------|--------------------------|
| /cart  | List items in cart       |

How would we implement this in our Rack app? The path lives in the HTTP request, and to get to that we have to inspect that `env` part of our `#call` function. In the `env` variable is all of the information contained in the request. Thankfully, Rack has a great way of parsing all this information for us. It looks like this:

```ruby
class Application

  def call(env)
    resp = Rack::Response.new
    req = Rack::Request.new(env)
    resp.finish
  end
  
end
```

This [Rack::Request](http://www.rubydoc.info/gems/rack/Rack/Request) instance now has a ton of useful methods. If we look through the documentation, it has a method called `#path`. This will return the path that was requested. Before we inspect the `#path` let's set up what we would do if they wanted to see all of our items:

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

Now we put all of your items out. Problem is this will list all of your items *no matter what path you put in*. Give it a try. Type `localhost:9292/items`, `localhost:9292/cart`, `localhost:9292/flatiron/is/awesome`. All of those URLs work. Right now, we don't filter for path. No matter what the request is, we send the same response. Let's filter so that this only works for the `/items` path using the `#path` method of our `Rack::Request` object:


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

Great! Now we are caring about the path. With this we can now do different things depending on the path.

### User Input Via The Path

What if users wanted to check and see if we have `Apples` available in our list of items? How do other websites handle getting user queries? If we go to GitHub and type in "apples" in the search query, we get a URL that looks like this: `https://github.com/search?q=apples`. Everything looks normal, domain of `github.com` path of `search`, and then there is the `?` character. After that character comes `q=apples`. There is our search! 

The section after the `?` are called the `GET` parameters. These are any number of key/value pair parameters. So the key in GitHub's case would be `q` and the value of `apples`. If you notice, `GET` params come in key/value pairs. The matching Ruby data structure that is also a key/value store would be a `Hash`! Thankfully, Rack provides the mechanism to parse the `GET` params and return them to us in a standard `Hash`. If we wanted to implement a `/search` route that accepted a `GET` param with the key `q` it would look something like this:


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
