---
layout: post
title: Proc.new(&block)という名の有難迷惑
categories: [ruby]
tags: [syntax, ninpo]
description: Proc.new(&block)への不満をつらつらと
---

いつもの如くgemリーディングを行っていたらこんなコードに出くわした

{% highlight ruby %}
def define_route path, options, &block
  self.route_defs << [path, options, Proc.new(&block)]
end
{% endhighlight %}

おや`Proc.new(&block)`て意味ないのでは..  
  

{% highlight ruby %}
def define_route path, options, &block
  self.route_defs << [path, options, block]
end
{% endhighlight %}

そもそも引数の`&block`で`Proc`に変換しているので、この様に`Proc.new`しなくても、そのまま変換されたものを渡せばいいのでは  
  

{% highlight ruby %}
Proc.new{ "kakuremi" }
{% endhighlight %}

`Proc.new`を使う場合はブロックを直接渡すか  

{% highlight ruby %}
def ninpo
  Proc.new
end

ninpo do
  "kakuremi"
end
{% endhighlight %}

こんな感じで`ninpo`に渡されたブロックを元に`Proc`を作成するのであって、この場合は`ninpo`の引数にブロックは必要ない。なので  

  

{% highlight ruby %}
def define_route path, options, &block
  self.route_defs << [path, options, block]
end
{% endhighlight %}

もしくは

{% highlight ruby %}
def define_route path, options
  self.route_defs << [path, options, Proc.new]
end
{% endhighlight %}

でも良い。ただ引数にブロックを明示的に宣言した方が、親切なので後者より前者が良いと思います。  
まぁでも先のコード書いた人の意図としては、Procオブジェクトであるのに`block`という変数名で配列に収めるのが気持ち悪かったのだろう。分からなくないでもありません。しかしブロックはオブジェクトではないので変数名が`block`である時点でそれはもう`Proc`なのですよ。それに「事情があって`Proc.new(&block)`で新たなオブジェクトを作ろうとしているのでは」とミスリーディングする恐れがある。  
  

{% highlight ruby %}
def ninpo &block
  block.equal? Proc.new(&block) # => true
end

ninpo do
  "kakuremi"
end
{% endhighlight %}

残念ながら`Proc.new`しても同じブロックが生成要素なら同じオブジェクトなのです。(rubyいいぞぉ〜〜〜)  
そもそも`Proc`より`lambda`のほうが好みだ。もっというなら`->`の方がかっこいい。脱線乙。  




