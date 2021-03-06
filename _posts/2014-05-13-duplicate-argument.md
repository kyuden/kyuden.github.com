---
layout: post
title: 引数の重複
categories: [ruby]
tags: [refactor, ninpo]
fullview: true
---

例えばこんなコード  

{% highlight ruby linenos %}
def csv_data(since_time, till_time)
  User.actice_user(since_time, till_time)
end

def data_count(since_time, till_time)
  csv_data(since_time, till_time).size
end

def run(since_time, till_time)
  write_csv_file csv_data(since_time, till_time)
end
{% endhighlight %}

`since_time`,`till_time`が引数として散在している。こんな場合はメジャーな策が主に2つあり、その判別法も割とシンプルだ。  まず考えるのが **重複しているデータがプレーンなデータか？何らか処理後(もしくは処理を行う予定の)のデータか？** だ。  

 * プレーンなデータの場合  
    `since_time`,`till_time`は他のクラスなどで生成されたデータで本クラスのメソッドに渡されているものとし、特に加工せず使い回すデータだとしましょう。そんなプレーンなデータである場合はデータをインスタンス変数として保持すると良いでしょう。具体的には  

{% highlight ruby linenos %}
def initialize(attr={})
  @since_time = attr[:since_time]
  @till_time  = attr[:till_time]
end

def csv_data
  User.actice_user(@since_time, @till_time)
end

def data_count
  csv_data.size
end

def run
  write_csv_file csv_data
end
{% endhighlight %}

インスタンス変数を使用した結果、見事に引数の重複はなくなりシンプルになりましたね。 **特に加工せず使い回しているデータ** というのが重要で、もし途中で処理し値が変化するようなデータだと、インスタンス変数で保持する前に少し踏みとどまって考えた方が良いでしょう。なぜならファイルが長くなった場合(それ自体がアンチパターンなのだが)などにどのタイミングでデータ値が変更されているか把握しづらいので、インスタンス変数の参照時点で、それはもう意図と異なる値かもしれません。  

 * 何らか処理後(もしくは処理を行う予定の)のデータの場合  
   `since_time`,`till_time`が他のクラスから渡され、本クラスで処理を行った後使い回す場合、もしくは本クラスで処理によって求めたデータであるとしましょう。そのような何らか処理後(もしくは処理を行う予定の)のデータの場合はメソッド化すると良いでしょう。具体的には  

{% highlight ruby linenos %}
def csv_data
  User.actice_user(since_time, till_time)
end

def data_count
  csv_data.size
end

def run
  write_csv_file csv_data
end

def since_time
   Date.today
end

def till_time
   Date.today - User.find_by(user_roul: 1, user_active: true).created_at
end
{% endhighlight %}

メソッドを使用した場合も同様に、重複なくシンプルにすることが出来ました。

{% highlight ruby linenos %}
def since_time
   @since_time ||= Date.today
end

def till_time
   @till_time ||= Date.today - User.find_by(user_roul: 1, user_active: true).created_at
end
{% endhighlight %}  

また場合にもよりますが`since_time`,`till_time`を取得するのにDBからの取得が伴ったり、重い処理で何度も実行させたくない場合は、この様にメモ化するのが一般的ですね。そもそもメソッド自体をなくしたりみたいな話になると魔術の世界になるので今日はここらでやめておく。
