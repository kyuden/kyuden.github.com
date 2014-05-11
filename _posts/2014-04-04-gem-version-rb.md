---
layout: post
title: my version.rb
categories: [ruby]
tags: [gem]
description: gemのversion.rbです....
---

gemのversion.rbです

{% highlight ruby %}
module M
  class Version
    MAJOR = 0
    MINOR = 9
    PATCH = 3
    PRE = nil

    class << self
      def to_s
        [MAJOR, MINOR, PATCH, PRE].compact.join('.')
      end
    end
  end
end
{% endhighlight %}
