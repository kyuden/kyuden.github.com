---
layout: post
title: Keep Finders on Their Own Model
categories: [rails]
tags: [antipatterns, rails3]
fullview: true
---

rails_antipatternsについて書いていきます

{% highlight ruby linenos %}
class UsersController < ApplicationController
  def index
    @user = User.find(params[:id])
    @memberships = @user.memberships.where(:active => true).
                                     limit(5).
                                     order("last_active_on DESC")
  end
end
{% endhighlight %}

[Solution: Push All find() Calls into Finders on the Model](kyuden.org/blog/2014/02/25/solution-push-all-find-calls-into-finders-on-the-model/)を適用してみよう

## [try1] Solution: Push All find() Calls into Finders on the Model
{% highlight ruby linenos %}
class UsersController < ApplicationController
  def index
    @user = User.find(params[:id])
    @recent_active_memberships = @user.find_recent_active_memberships
  end
end
{% endhighlight %}

{% highlight ruby linenos %}
class User < ActiveRecord::Base
  has_many :memberships

  def find_recent_active_memberships
    memberships.where(:active => true).
                limit(5).
                order("last_active_on DESC")
  end
end
{% endhighlight %}

## AntiPattern
`User`Modelで`Membership`Modelのfinderを記述すべきでない。
自身のModelのドメインを明確に。

## [try2] move Membership Model finder into the Membership Model From the User Model
{% highlight ruby linenos %}
class User < ActiveRecord::Base
  has_many:memberships

  def find_recent_active_memberships
    memberships.find_recently_active
  end
end
{% endhighlight %}

{% highlight ruby linenos %}
class Membership < ActiveRecord::Base
  belongs_to :user

  def self.find_recently_active
    where(:active => true).limit(5).order("last_active_on DESC")
  end
end
{% endhighlight %}

`where(:active => true)`とか`order("last_active_on DESC")`のクエリメソッドは他でも結構使えそう

## [try3] use the named scope of rails
{% highlight ruby linenos %}
class User < ActiveRecord::Base
  has_many :memberships

  def find_recent_active_memberships
    memberships.only_active.order_by_activity.limit(5)
  end
end
{% endhighlight %}

{% highlight ruby linenos %}
class Membership < ActiveRecord::Base
  belongs_to :user
  scope :only_active, where(:active => true)
  scope :order_by_activity, order('last_active_on DESC')
end
{% endhighlight %}

すっきりー
