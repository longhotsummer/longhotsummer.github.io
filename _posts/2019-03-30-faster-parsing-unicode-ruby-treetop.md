---
layout: post
title: Faster Parsing of Unicode Strings with Ruby Treetop
date: 2019-03-30
---

At [Laws.Africa](https://laws.africa) we rely heavily on [Treetop](https://rubygems.org/gems/treetop/versions/1.6.10), a Ruby library for writing parsers. Treetop is part of the [Slaw library](https://github.com/longhotsummer/slaw/) and helps our [Indigo legislation platform](https://laws.africa/indigo/) to transform plain text legislation into richly structured machine-friendly XML in Akoma Ntoso format.

We recently found that parsing larger Acts (100KB or more of plain text) was taking longer than we expected. Parsing a 1MB plain text Act took about an hour!

It turns out that this is due to the fact indexing into UTF-8 encoded Unicode strings in Ruby takes linear time and not constant time (as it is with ASCII strings). This means that the time to parse a UTF-8 string grows exponentially as the size of the string increases. The same doesn't apply to ASCII encoded strings where indexing and substring extraction is constant time, independent of the length of the string.

There is more detail in [the discussion of this issue on GitHub](https://github.com/cjheath/treetop/issues/31) but suffice it to say this is a long-standing issue with unicode strings in Ruby.

### Workaround for parsing unicode strings

Our use case requires that we're able to support UTF-8 encoded input, so forcing our strings to be ASCII is not an option. So we need a way to downcode our input to ASCII encoding, without losing those unicode characters that can't be represented in ASCII directly.

Luckily, precisely this problem has been solved for encoding URLs: "unsafe" and other non-ASCII characters are %-encoded in URLs, such as `euro=€&pound=£` being encoded to `euro=%E2%82%AC&pound=%C2%A3`.

We can take advantage of Ruby's `URI::escape` method and provide it with a custom set of "unsafe" characters: in our case everything in ASCII is safe except for `%` itself, which also needs to be escaped. Luckily, our Treetop grammar doesn't match any unicode characters or `%`.

Here's how we escape non-ASCII characters and re-encode the string as ASCII:

{% highlight ruby %}
require 'uri'
unsafe = (0..126).to_a - ['%'.ord]
unsafe = unsafe.map { |i| '\u%04x' % i }
unsafe = Regexp.new('[^' + unsafe.join('') + ']')
URI::DEFAULT_PARSER.escape(text, unsafe)
{% endhighlight %}

Our parser produces XML output which we can then unescape to restore the UTF-8 encoding.

{% highlight ruby %}
URI.unescape(xml)
{% endhighlight %}

You can see the details of how we do it in Slaw [in this commit)[https://github.com/longhotsummer/slaw/commit/8386b8416accb2f71416760c3c25d995c4565dd7].

### How much faster, exactly?

The results of this change are enormous and hugely beneficial to our users:

* time to parse 100KB of input drops from 24 seconds to three seconds
* time to parse 1MB of input drops from one hour to two minutes

