---
layout: default
modal-id: 3
date: 2015-06-29
img: blocmetrics.png
alt: image-alt
project-date: June 2015
client: Start Bootstrap
category: Web Development
description: Use this area of the page to describe your project. The icon above is part of a free icon set by <a href="https://sellfy.com/p/8Q9P/jV3VZ/">Flat Icons</a>. On their website, you can download their free set with 16 icons, or you can purchase the entire set with 146 icons for only $12!

---

>To help website owners to get informed of the traffic of their websites, Blocmetrics provides simple statistics to the call of a website by inserting a client-side JS snippet in target web page and showing statistical chart on Blocmetrics side

#Explanation
Blocmetrics provides free web analysis and reporting by a client-side JS snippet that sends data when the web page including it is called, a server-side API that captures and save this information to database, and a front-end graph showing the statistical chart based-on database.
The client-end JS snippet is a simple function `blocmetrics.report()` whose source code can be called online or parse directly into the user’s web page. Once the page is called, `blocmetrics.report()` send customerized information to Blocmetrics. The server-side API is actually a controller having a certain route, the controller receives and saves the data when the route is called properly. Finally, we use Chartkick library to present the data on pie chart, bar chart, or line chart.

#Problem
Sending data from one web app to another always full of uncertain. One major error is Cross-Origin Request Blocked, caused by sending an unauthorized request from one website to the other, which is blocked by rails defaultly in order to prevent risk. The other is No Route Matches Error on Blocmetrics side. We tried to send a POST request to Blocmetrics and we have corresponding routes on server-side, however, we still got this error.

#Solution
We added [a piece of code](https://gist.github.com/dhoelzgen/cd7126b8652229d32eb4) to walk around CORS

{% highlight ruby%}
 skip_before_filter :verify_authenticity_token
 before_filter :cors_preflight_check
 after_filter :cors_set_access_control_headers
 def cors_set_access_control_headers
   headers['Access-Control-Allow-Origin'] = '*'
   headers['Access-Control-Allow-Methods'] = 'POST, GET, PUT, DELETE, OPTIONS'
   headers['Access-Control-Allow-Headers'] = 'Origin, Content-Type, Accept, Authorization, Token'
   headers['Access-Control-Max-Age'] = "1728000"
 end

 def cors_preflight_check
   if request.method == 'OPTIONS'
     headers['Access-Control-Allow-Origin'] = '*'
     headers['Access-Control-Allow-Methods'] = 'POST, GET, PUT, DELETE, OPTIONS'
     headers['Access-Control-Allow-Headers'] = 'X-Requested-With, X-Prototype-Version, Token'
     headers['Access-Control-Max-Age'] = '1728000'
     render :text => '', :content_type => 'text/plain'
   end
 end
{% endhighlight %}

After checking the server log, we found that what client-side send is not a POST request but a OPTION request, so we add this code to `route.rb` to allow the request.

{% highlight ruby%}
match 'events.json' => "events#create", via: :options, as: :events_options
{% endhighlight %}
