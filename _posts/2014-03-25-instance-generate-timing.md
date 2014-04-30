---
layout: post
title: インスタンス変数生成のタイミング
categories: [ruby]
tags: [syntax]
description: インスタンス変数は値が代入された時に初めて生成されます....
---

インスタンス変数は値が代入された時に初めて生成されます。
なので同じクラスのオブジェクトでもインスタンス変数の数が異なることが十分にあり得ますね

{% highlight ruby %}
class MyClass
  def create
    @test = "hello"
  end
end

obj = Myclass.new
p obj.instance_variables
p obj.create
p obj.instance_variables
{% endhighlight %}

{% highlight console %}
[]
"hello"
[:@test]
{% endhighlight %}
