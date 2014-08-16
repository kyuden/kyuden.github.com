---
layout: post
title:  アクセサとインスタンス変数
categories: [ruby, book]
tags: [refactoring]
fullview: true
---

アクセサを定義するか、インスタンス変数に直接アクセスするか

アクセサは単なるgetterかどうか確認する必要があるので,インスタンス変数のほうが可読性は高い.  
しかし、スーパークラスのインスタンス変数値をサブクラスで変更したい場合は、
アクセサを定義しておくとオーバーライドで変更できるのでスーパークラスのメソッドをそのまま使用することができる.  


{% highlight ruby %}
class Item
  def initialize(base_price, tax_rate)
    @base_price = base_price
    @tax_rate   = tax_rate
  end

  def total
    @base_price * (1 + @tax_rate / 100.0)
  end
end

class ImportedItem < Item
  def initialize(base_price, tax_rate, import_duty)
    super(base_price, tax_rate)
    @import_duty = import_duty
  end

  def total
    @base_price * (1 + (@tax_rate + @import_duty) / 100.0)
  end
end
{% endhighlight %}

税抜き価格　+ 税率　の定義が変更したわけでなく税率が加算され変化しただけなので上記の様に`total`を再定義するのは、保守面からも問題がある。

{% highlight ruby %}
class Item
  attr_accessor :base_price, :tax_rate

  def initialize(base_price, tax_rate)
    @base_price = base_price
    @tax_rate   = tax_rate
  end

  def total
    base_price * (1 + tax_rate / 100.0)
  end
end

class ImportedItem < Item
  attr_reader :import_duty

  def initialize(base_price, tax_rate, import_duty)
    super(base_price, tax_rate)
    @import_duty = import_duty
  end

  def tax_rate
    super + import_duty
  end
end
{% endhighlight %}


スーパークラスで`tax_rate`のアクセサを定義したことで、オーバーライド可能となり`total`を再定義せずに済んだ。
