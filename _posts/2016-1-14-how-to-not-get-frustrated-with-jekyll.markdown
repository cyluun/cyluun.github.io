---
layout: post
title:  "How to not get frustrated with Jekyll"
date:   2016-1-14
description:  I don't understand how Jekyll works. Which probably translates to "I don't understand how Front Matter or Liquid works".
---

If you're trying to get your blog set up with Jekyll there is a very likely case that at some point you will find yourself facepalming in despair. Well, I'm here to save you some time and address some of the problems you might bump into.

Assuming you have installed Jekyll (you can find plenty of tutorials on that online) and you served your site on your localhost, take a tour inside the site's directory. You want it to look something like that:

{% highlight bash %}
|_config.yml
|_includes
├── footer.html
└── header.html
|_layouts
├── default.html
└── post.html
|css
└── main.css
|_posts
└── 2015-12-12-welcome-to-jekyll.markdown
|_site
index.html
{% endhighlight %}

<br>

## Problem no.1: I don't understand how Jekyll works.

Which probably translates to "I don't understand how Front Matter or Liquid works". Jekyll is essentially a bunch of "includes". One file inherits from the other with a combination of Front Matter and Liquid tags.

Starting from the beginning, the `index.html` in your root folder is where everything happens. Instead of putting your html there though, you just need to point to a layout you want for your main page. You do this by using Front Matter [like so][front-matter]. So it will look like that:

{% highlight bash %}
---
layout: default
---
{% endhighlight %}

This means that whatever is in the `default.htlm` file is now in the `index.html`. This was Front Matter.

<br>

### What it all means

* `_layouts` is where you create all your different views for your site. A view for your home, another for your post pages, maybe another for your about page.
* `_includes` is where you put the html for your header, footer, navigation bar, etc that you will reuse in your different pages.
* `_site` is where Jekyll does the build and should not require your attention.
* `_config.yml` is a file where you fill in some basic info that will act as variables and will be usable throughout the site.
* `_posts` is where your markdown posts will be, and where you will create them.

<br>

## Problem no.2: I can't display my posts.

If you want to display your posts in your main page, you need to insert Liquid tags like {% raw %}`{{ post.title }}` and `{{ post.content }}`{% endraw %} into your html.

An example of displaying a snippet:

{% highlight html %}
{% raw %}
{% for post in site.posts limit 10 %}
<li>
    {{ post.date | date_to_string }}
    {{ post.title }}
</li>
<p>{{ post.content | strip_html | truncatewords:30}}</p>
<a href="{{ post.url }}">Read more...</a>
{% endfor %}
{% endraw %}
{% endhighlight %}

If you are displaying your posts in another page other than your homepage, make sure that in the layout html file that's intended for your posts page there is a Liquid tag {% raw %}`{{ content }}`{% endraw %}.

<br>

## Problem no.3: Where is my CSS?

Linking is a bit tricky with Jekyll. The chances you will lose your CSS are kinda high. You can prevent this by adding a forward slash at the start of each link as in `/css/syntax.css` or {% raw %}`{{ site.url }}/css/syntax.css`{% endraw %}.

> Also make sure that your `post.html` inside your layouts has the proper Front Matter on the top, or that essentially all the parts of the html file are being somehow passed through.

<br>

## But a picture is worth a thousand words

Here is an example build:

`/_layouts/frontpage.html` is your home page:

{% highlight html %}
{% raw %}
<!DOCTYPE html>
<html lang="en">

{% include head.html %}

    <body>
      {% include nav.html %}
      {% include header.html %}
      {% include call-to-action.html %}
      {% include blog-placeholder.html %}
      {% include services.html %}
      {% include portfolio.html %}
      {% include footer.html %}
      {% include scripts.html %}
    </body>

</html>

{% endraw %}
{% endhighlight %}

Everything that's included is located inside the `_includes` folder.

`/_layouts/default.html` is your blog page:

{% highlight html %}
{% raw %}
<!DOCTYPE html>
<html>

  {% include head.html %}

  <body>

    {% include nav.html %}
    {% include blog-header.html %}

    <div class="container">
        <div class="page-content">
          <div class="wrapper">
            {{ content }}
          </div>
        </div>
    </div>

    {% include footer.html %}

  </body>

</html>


{% endraw %}
{% endhighlight %}
<br>

`_layouts/post.html` is your post content:

{% highlight html %}
{% raw %}
---
layout: default
---
<article class="post">

  <div class="post-content">
    {{ content }}
  </div>

</article>

{% endraw %}
{% endhighlight %}

And by having the proper Front Matter at the start of your individual post files for example `2015-12-12-welcome-to-jekyll.markdown`, everything works!

**Tip**: Jekyll's docs can prove to be confusing. Learning hands on by building first and thinking later won't be less confusing but it might save you some time!

[jekyll-layouts]: http://jekyll.tips/guide/layouts/
[front-matter]: http://jekyll.tips/guide/front-matter-and-liquid/
