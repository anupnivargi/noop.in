---
layout: post
title:  "Using Rack Middleware for Autocompletion in Rails"
date:   2012-03-04
---

In todays modern web applications auto completetion/ auto suggestions are very common right from a small to very large applications, serving up multiple requests simultaneously with quick response from the server is very important to make the user experience better and enjoyable.  
Here is where rack middleware comes in the picture. 

So what is Rack Middleware?  
In simple terms a Rack middleware allows us to do things before or after in a rack application. Rack middleware is initialized using a rack application and should respond to a call method. Rack middleware can further hand over the control to other rack middleware or just respond back with a response code, content-type and body.
{% highlight ruby %}
[200, {'Content-type' => 'text/html'},['Hello Web']]
{% endhighlight %}

So how can you use this in a Rails Application?  

Rails already has a predefined set of Rack middlewares for caching, session handling etc, you can look at the current middlewares by running
rake middleware.  
Apart from this Rails also provides a great way to add your own Rack middleware to your application.  
Using a Rack middleware to handle auto completes is great, as it gives you a slight performance boost as compared to handling the request in a Rails controller.

So lets look at some code as to how to achieve this.

Requirement
- User should be authenticated
- Respond with json with the result of the query
- Handle the response on the client side with javascript 

### Rack Middleware ###
Make a directory for middlewares in your rails application  
{% highlight ruby %}
mkdir app/middleware  
{% endhighlight %}
Make a new file app/middleware/auto_completer.rb 

{% highlight ruby linenos %}
class AutoCompleter
  def initialize(app)
    @app = app
  end

  def call(env)
    @request = Rack::Request.new(env)
    if env["PATH_INFO"] == "/autocomplete"
      session = @request.env['rack.session']
      params = @request.params
      if session && session["user_id"].present?
        users = User.select("id, name").like(params["q"])
        [200, {"Content-Type" => "application/json"}, [users.to_json]]
      else
       [404, {"Content-Type" => "text/html"}, ["Not Found"]]
      end
    else
      @app.call(env)
    end
  end
end
{% endhighlight %}

The above code is initialized with a instance of rack application, then handing the control to the call method.
In the call method we check if the request path matches autocomplete,
we then check the session and find matching users and return a json with a 200 response.

If the request does not match the autcomplete request path we hand over the control to the other rack middleware. 

Edit your config/application.rb to configure Rails to pick up the new middleware
{% highlight ruby %}
config.middleware.insert_after ActionDispatch::Session::CookieStore, "AutoCompleter"
{% endhighlight %}
This tells rails to insert the new middleware we have made after the session middleware.

Run the command again to see you can see the new middleware got added after ActionDispatch::Session::CookieStore
rake middleware 
Start your Rails server and goto localhost:3000/autocomplete?q=h
Expected Response
{% highlight json %}
[{"id":2,"name":"Harry"},{"id":4,"name":"Harold"}]
{% endhighlight %}

### Javascript ###
We will be using jquery-ui plugin for autocompletion.
Include jquery-ui in assets/javascript/application.js

{% highlight javascript linenos %}
//= jquery-ui

$(function() {
  $.ui.autocomplete.prototype._renderItem = function (ul, item) {
  label = item.name.replace(new RegExp("(?![^&;]+;)(?!<[^<>]*)(" + $.ui.autocomplete.escapeRegex(this.term) + ")(?![^<>]*>)(?![^&;]+;)", "gi"), "<strong>$1</strong>");
  return $("<li></li>")
  .data("item.autocomplete", item)
  .append("<a>" + label + "</a>")
  .appendTo(ul);
  };

  $("#autocomplete" ).autocomplete({
    source: function(req,res){
      $.getJSON("/autocomplete", { q : req.term }, res)
    },
    minLength: 1,
    select: function( event, ui ) {
      $("#selected").append("
      <b>Id: " + ui.item.id + " Name: " + ui.item.name + "</b>"); 
    }
  });
}); 
{% endhighlight %}

The first piece of code patches the renderItem method in jQuery-ui auto complete plugin to use the json format we are sending from the server.
The second part is pretty straight forward where we hook the auto complete call to a html element id in the DOM.
Notice we have used "q" as the name of the query parameter instead of the default "term" parameter.  

### Rails Controller and View ###
Generate a new controller scaffold
{% highlight bash %}
rails generate controller dash index
{% endhighlight %}

Edit a new template dash/index.html.erb
{% highlight ERB %}
<h1>Todo App</h1>
<p>Autocomplete
<%= text_field_tag :user_id, nil, :id => "autocomplete" -%>
</p>
<div id="selected"></div>
{% endhighlight %}

Start the server and goto http://localhost:3000/dash  
And Thats it. 

### Resources ###
[Rack Middleware and Rack Applications: What's the Difference?](http://intridea.com/blog/2010/4/20/rack-middleware-and-applications-whats-the-difference)  
[Introduction to Rack Middleware](http://amberbit.com/blog/introduction-to-rack-middleware)
