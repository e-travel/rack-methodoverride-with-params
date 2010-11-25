dzello's fork of Rack MethodOverride With Params
===============================
This version allows you to override any request method. So,
even if the client makes a GET request, if a _method param
exists, it will be used.

If you are building an application that relies on receiving
cross domain requests, right now you really have to stick with
making GET requests from the client. Even with proper CORS headers,
browsers still will not behave properly - the specific problem
I ran into is that they will not send cookies in a cross-domain
POST - the GET works fine in IE8+, FFox 3.5+, and in Safari/Chrome.

So, if you want to keep your Rails App RESTful and work around theis
issue (until "Access-Control-Allow-Credentials" works properly),
one solution is to send everything as a GET, and override as necessary.

See more about CORS at http://http://www.nczonline.net/blog/2010/05/25/cross-domain-ajax-with-cross-origin-resource-sharing/

To use this with Rails and the optional "override_any_methods" configuration:

  require File.expand_path('../../lib/rack/rack-methodoverride-with-params', __FILE__)

  Scrappz::Application.configure do
    config.middleware.delete Rack::MethodOverride
    config.middleware.insert_before ActionDispatch::Head, Rack::MethodOverrideWithParams, :override_any_method => :true
  end

Place this in your environment.rb. If you have a custom Rack setup, you might have to tweak the middleware
that you insert_before. However, since middleware.swap didn't want to take options (I don't think?)
I did it this way with the delete/insert.

Note: Rails 3 logging still shows the original method - so if you override to _method=POST you'll
see a GET in the log, yet your RESTful create method will get called.


Rack MethodOverride With Params
===============================
Rack MethodOverride With Params solves the issue where POST requests with `_method` as a query param do not use `_method`'s value as the HTTP method in Rails etc that use Rack MethodOverride.

It does this by extending `Rack::MethodOverride` and overriding it so it looks at both query params and form params. So you can use it anywhere you were using `Rack::MethodOverride`

To use it with Rack Builder

    require 'rack-methodoverride-with-params'
    #...
    use Rack::MethodOverrideWithParams

to use it with Rails

add it to the `Gemfile`

    gem 'rack-methodoverride-with-params'

swap Rack::MethodOverride in `config/environment.rb`

    config.middleware.swap Rack::MethodOverride, Rack::MethodOverrideWithParams
Contrib
---------------
There is not much here, but if you want to play around, clone the project, run the specs and hack away.

I probably won't be actively working on this much as it is really simple, but if you ping me about it on github or twitter(I'm baroquebobcat), I'll help you out.