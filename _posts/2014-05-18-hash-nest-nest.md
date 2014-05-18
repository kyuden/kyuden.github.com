---
layout: post
title: hashのnest, nest, nest...
categories: [ruby]
tags: [ninpo]
description: N階層のネストhashを簡単に作る
---

N階層でネストさせるhashの宣言はこんな感じに書くと良い

{% highlight ruby %}
hash = Hash.new {|h,k| h[k] = Hash.new(&h.default_proc) }
hash[:get][:path] = '/' # => { get: {path: '/'} }
{% endhighlight %}

Hashは保持していないkeyが参照されるとHash.newに渡されたblockを実行する。トリックの種は`default_proc`でこいつはレシーバのblockを返すので`&`で再度blockとしているだけ。これでN階層のhashを手軽に作成できる。  

これ書いてて思い出したのがコロンブスの卵だ。  
1942年、アメリカ大陸を航海中に発見したコロンブスを「ただ航海中にたまたま見つけただけじゃないか。」と批判する人々に対し、「卵を立てられるか」と問う。誰も立てる事ができなかったが、コロンブスは卵の尻を凹ませて、立てたせ一言。「何事も、後から種を明かされたら簡単に見えるものなのですよ。」

