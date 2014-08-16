---
layout: post
title:  データ値からオブジェクトへ
categories: [ruby, book]
tags: [refactoring]
fullview: true
---

データ値が整形や値の抽出などで特別な振る舞いが増えてきた場合はデータ値をオブジェクトにするのが良い

{% highlight ruby %}
class Order
  def initialize(customer)
     @customer  = customer
  end

  def customer_full_name
    @customer
  end

  def customer_first_name
    @customer.split(" ").first
  end

  def customer_family_name
    @customer.split(" ").last
  end
end
{% endhighlight %}

Orderクラスに属性(customer)を操作する処理が復数定義されている

{% highlight ruby %}
class Order
  def initialize(customer_name)
     @customer  = Customer.new(customer_name)
  end

  def customer
    @customer
  end

  def customer=(name)
    @customer = Customer.new(name)
  end
end

class Customer
  def initialize(name)
    @name = name
  end

  def name
    @name
  end

  def first_name
    @name.split(" ").first
  end

  def family_name
    @name.split(" ").last
  end
end
{% endhighlight %}

属性をクラスに昇格されて、操作メソッドをクラス内で定義する
