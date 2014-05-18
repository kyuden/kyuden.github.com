---
layout: post
title: Push All find() Calls into Finders on the Model
categories: [rails]
tags: [antipatterns, rails3]
fullview: true
---

rails_antipatternsについて書いていきます

{% highlight html %}
<html>
<body>
  <ul>
     <% User.find(:order => "last_name").each do |user| -%>
      <li><%= user.last_name %> <%= user.first_name %></li>
    <% end %>
  </ul>
</body>
</html>
{% endhighlight %}
* View内で直接DBにアクセスすべきでない

### [Try1] move the logic into the Controller

{% highlight ruby %}
class UsersController < ApplicationController
  def index
    @users = User.order("last_name")
  end
end
{% endhighlight %}

{% highlight html %}
<html>
<body>
  <ul>
    <% @users.each do |user| -%>
      <li><%= user.last_name %> <%= user.first_name %></li>
    <% end %>
  </ul>
</body>
</html>
{% endhighlight %}

### AntiPattern
* DBのfinderはModelで記述すべき

### [Try2] move the direct find call down into the Model
{% highlight ruby %}
class UsersController < ApplicationController
  def index
    @users = User.ordered
  end
end
{% endhighlight %}
{% highlight ruby %}
class User < ActiveRecord::Base
  def self.ordered
    order("last_name")
  end
end
{% endhighlight %}

railsではさらに美しく記述できる機能が用意されている

### [try3] use the named scpoe of rails
{% highlight ruby %}
calss User < ActiveRecord::Base
  scope :orderd, order("last_name")
end
{% endhighlight %}

ちょーきもちー
