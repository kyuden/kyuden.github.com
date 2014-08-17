---
layout: post
title:  ハッシュからオブジェクトへ
categories: [ruby, book]
tags: [refactoring]
fullview: true
---

書籍「リファクタリングruby」8.6　ハッシュからオブジェクトへ  
異なる種類のオブジェクトを格納し、復数の目的のために渡され使用されるhashはhashのキーに何が格納されているか分からなくなる。ということでオブジェクトする。

{% highlight ruby %}

class NetWork
  attr_reader :name

  def initialize(name)
    @name = name
  end
end

class Node
  attr_reader :new_network
  def initialize(network)
    @new_network = network
  end
end

a_net, b_net, c_net  = NetWork.new("a_net"), NetWork.new("b_net"), NetWork.new("c_net")
a_node, b_node, c_node = Node.new(a_net), Node.new(b_net), Node.new(c_net)

new_network = { nodes: [], old_networks: [] }
new_network[:old_networks] << a_node.new_network
new_network[:nodes] << a_node

new_network[:old_networks] << b_node.new_network
new_network[:nodes] << b_node

new_network[:old_networks] << c_node.new_network
new_network[:nodes] << c_node

new_network[:name] = new_network[:old_networks].collect {|net_work| net_work.name}.join(" - ")

{% endhighlight %}

`new_network`は異なる種類のオブジェクトが格納されている

{% highlight ruby %}

class ResultNetwork
  attr_accessor :old_networks, :nodes

  def initialize
    @nodes, @old_networks = [], []
  end

  def [](attribute)
    instance_variable_get("@#{attribute}")
  end

  def []=(attribute, value)
    instance_variable_set("@#{attribute}", value)
  end
end

new_network = { nodes: [], old_networks: [] }
new_network[:old_networks] << a_node.new_network
new_network[:nodes] << a_node

new_network[:old_networks] << b_node.new_network
new_network[:nodes] << b_node

new_network[:old_networks] << c_node.new_network
new_network[:nodes] << c_node

new_network[:name] = new_network[:old_networks].collect {|net_work| net_work.name}.join(" - ")

new_network = ResultNetwork.new

{% endhighlight %}

ひとまず`instance_variable_get` `instance_variable_set`を使用しhashをオブジェクトに置き換えても既存処理が通るようにする。

{% highlight ruby %}

class ResultNetwork
  attr_accessor :old_networks, :nodes, name

  def initialize
    @nodes, @old_networks = [], []
  end
end

new_network.old_networks << a_node.new_network
new_network.nodes << a_node

new_network.old_networks << b_node.new_network
new_network.nodes << b_node

new_network.old_networks << c_node.new_network
new_network.nodes << c_node

new_network.name = new_network.old_networks.collect {|net_work| net_work.name}.join(" - ")

{% endhighlight %}

置き換える

{% highlight ruby %}

class ResultNetwork
  attr_accessor :old_networks, :nodes

  def initialize
    @nodes, @old_networks = [], []
  end

  def [](attribute)
    instance_variable_get("@#{attribute}")
  end

  def []=(attribute, value)
    instance_variable_set("@#{attribute}", value)
  end

  def name
    old_networks.collect {|network| network.name}.join(" - ")
  end
end

  new_network = ResultNetwork.new

  new_network.old_networks << a_node.new_network
  new_network.nodes << a_node

  new_network.old_networks << b_node.new_network
  new_network.nodes << b_node

  new_network.old_networks << c_node.new_network
  new_network.nodes << c_node

  new_network.name

{% endhighlight %}

`name`メソッドを移すことができ、いっそうすっきり


