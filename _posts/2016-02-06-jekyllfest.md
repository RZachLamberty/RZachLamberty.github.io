---
layout: post
title: Dr. Jekyll and Mr. Hide the Details
excerpt: "Jekyll ain't so bad once you get to know him"
keywords: "github, jekyll, blog, liquid, template, formatting, markup, markdown, markorubio, learning"
---

I spent a bit of time revamping my github site today, and in particular this fun little snippet:

{% highlight html %}{% raw %}
{% if page.series %}
<div class="series">
  {% assign sorted_series = (site.posts | where: "series", page.series | sort: "part")%}
  <p>This article is part {{page.part}} in a series on <strong>{{page.series}}</strong>. If this is your bag, check out</p>
  <ul>
    {% for post in sorted_series %}
      <li>
        {% if post.url != page.url %}<a href="{{ site.baseurl }}{{ post.url }}">Part {{post.part}}</a>{% else %}Part {{post.part}}{% endif %}: {{post.excerpt}}
      </li>
    {% endfor %}
  </ul>
</div>
{% endif %}
{% endraw %}{% endhighlight %}

There are a few cool things going on here

1. I learned about the ability to filter and sort lists inside the markdown command, and though the syntax isn't super intuitive, it's compact and simple and piped. Pass the pipes, I always say, ask around.
2. As long as I'm disciplined in my `yaml` configuration of my post files, I have a static, built-in way of creating mini-series from my posts on the fly. That's pretty dope.

For reference, the required `yaml` snippet at the top of any given series blog post is simply:

{% highlight yaml %}
---
layout: post
title: my funny title
series: series_name
part: series_number
---
{% endhighlight %}
