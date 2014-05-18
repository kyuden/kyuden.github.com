---
layout: post
title: Follow the Law of Demeter
categories: [rails]
tags: [antipatterns, rails3]
fullview: true
---

rails_antipatternsについて書いていきます

{% highlight ruby %}
class Address < ActiveRecord::Base
  belongs_to :customer
end

class Customer < ActiveRecord::Base
  has_one :address
  has_many :invoices
end

class Invoice < ActiveRecord::Base
  belongs_to :customer
end
{% endhighlight %}

{% highlight haml %}
= @invoice.customer.name
= @invoice.customer.address.street
= @invoice.customer.address.city
= @invoice.customer.address.state
= @invoice.customer.address.zip_code
{% endhighlight %}

## AntiPattern

* `street`や`city`を取得するために`customer`オブジェクトをまたぐ記法で記述すべきでない

可読性(readability)、保守性(maintainability)の観点であまりよろしくない。`customer`オブジェクトをまたぐのはあくまで`street`や`city`を取得する手段であり、コードを読み進めていく上で必ずしも必要な情報ではない。また`customer`が`billing address` と `shipping address`の両方を持つようになった場合、修正箇所が多くなる。同時に記述量も多くなる

## Solution the Law of Demeter

  ここでthe Law of Demeterですよ。Law of Demeterは1987年にNortheastern Universityで提唱された概念で「AオブジェクトがBオブジェクトのmethodをcallするために、Bオブジェクトにアクセスすべきではない」というもので簡単に言うと「`.`は一回しか使うな」ってこと

## Follow the Law of Demeter

{% highlight ruby %}
class Address < ActiveRecord::Base
  belongs_to :customer
end
{% endhighlight %}

{% highlight ruby %}
class Customer < ActiveRecord::Base has_one :address
  has_many :invoices

  def street
    address.street
  end

  def city
    address.city
  end

  def state
    address.state
  end

  def zip_code
    address.zip_code
  end
end
{% endhighlight %}

{% highlight ruby %}
class Invoice < ActiveRecord::Base belongs_to :customer
  def customer_name
    customer.name
  end

  def customer_street
    customer.street
  end

  def customer_city
    customer.city
  end

  def customer_state
    customer.state
  end

  def customer_zip_code
    customer.zip_code
  end
end
{% endhighlight %}

{% highlight haml %}
= @invoice.customer_name
= @invoice.customer_street
= @invoice.customer_city
= @invoice.customer_state
= @invoice.customer_zip_code
{% endhighlight %}

これじゃModelがcolumnのmethodだらけになって膨れてく

## Use  `delegate` of rails

{% highlight ruby %}
class Address < ActiveRecord::Base
  belongs_to :customer
end
{% endhighlight %}

{% highlight ruby %}
class Customer < ActiveRecord::Base
  has_one :address
  has_many :invoices

  delegate :street, :city, :state, :zip_code, :to => :address
end
{% endhighlight %}

{% highlight ruby %}
class Invoice < ActiveRecord::Base
  belongs_to :customer

  delegate :name,
           :street,
           :city,
           :state,
           :zip_code,
           :to => :customer, :prefix => true
end
{% endhighlight %}

{% highlight haml %}
= @invoice.customer_name
= @invoice.customer_street
= @invoice.customer_city
= @invoice.customer_state
= @invoice.customer_zip_code
{% endhighlight %}

ちょーきもちい

