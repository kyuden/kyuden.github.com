---
layout: post
title: instance_evalとblock
categories: [ruby]
tags: [ninpo]
fullview: true
---

`Block`はクロージャを期待する場合と, 単なるblockスタイルで分かりやすく手続きを書きたいという2つの期待があると思ってる。(両方期待する場合もある)  

{% highlight ruby %}
get '/' do
  "Hello Sinatra"
end
{% endhighlight %}

今日はSinatraようにblockスタイルで分かりやすく手続きを書きたい場合について書いていく。 大凡このような場合、blockを宣言したクラスはプロキシの責務を負うようなクラスで、実際blockを処理するのはサービス層に分類される別クラスだったりする。実現方法としてまず思いつくのは`instance_eval`だ

{% highlight ruby %}
class Iga
  def kuchiyose &block
    Dragon.new("DragonFire").nin &block
  end
end

class Dragon
  attr_reader :waza

  def initialize(waza)
    @waza = waza
  end

  def nin &block
    instance_eval(&block)
  end
end
{% endhighlight %}

{% highlight ruby %}
Iga.new.kuchiyose do
  "Ninpo: #{waza}" # => "Ninpo: DragonFire"
end
{% endhighlight %}

`Iga`忍者は口寄せの術を使い、龍を召喚しドラゴンフレイムの攻撃に成功している。口寄せなので`Iga`クラスがドラゴンフレイムの保持はしておらず、`Dragon`クラスが保持する。
ポイントは`Iga`クラスのインスタンスで束縛されているblockが`instace_eval`によって`Dragon`クラスの環境で実行できている事だ。  
こういうTipsを有名なライブラリは当たり前のように使っているが、なんせ名前付けされてないので読めはするものの、書くとなるとなかなか思いつかなかったりする。アプリケーションエンジニアとはこういうTipsを積み重ね自在にコードを操るスキルも必要だと思う。インフラエンジニアも巷で話題のフルスタックエンジニアもコードは書けるのだし。
