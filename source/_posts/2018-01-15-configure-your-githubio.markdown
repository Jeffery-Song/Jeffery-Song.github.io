---
layout: single
title: "Configure ur own github page"
date: 2018-01-15 15:50:41 +0800
---

## Layouts

Layouts decides in what kind of format, each of your page will be presented.

In folder `_layouts` you can make your own layouts (the default layout is `page`). 

Create `default.html` inside `_layouts` and add these into it:

{% raw %}
```html
<!DOCTYPE html>
    <html>
        <head>
            <!-- for each post, display its title -->
            <title>{{ page.title }}</title>
            <!-- link to main stylesheet -->
            <link rel="stylesheet" type="text/css" href="/css/main.css">
        </head>
        <body>
            <nav>
                <ul>
                    <li><a href="/">Home</a></li>
                    <li><a href="/about">About</a></li>
                    <li><a href="/cv">CV</a></li>
                    <li><a href="/blog">Blog</a></li>
                </ul>
            </nav>
            <div class="container">
            <!-- then the post's content -->
            {{ content }}
            
            </div><!-- /.container -->
            <footer>
                <ul>
                    <!-- replace these with your own informatiosn -->
                    <li><a href="cow0712@mail.ustc.edu.cn">email</a></li>
                    <li><a href="https://github.com/Jeffer-Song">github.com/Jeffery-Song</a></li>
                </ul>
            </footer>
        </body>
    </html>
```
{% endraw %}

## Posts

Create a `_post` directory. All your posts should be placed inside here and named as `YYYY-MM-DD-title-of-post.md`(or markdown, textile, see [Jekyll posts](jekyllcn.com/docs/posts/)).

The post file should looks like this:

```markdown
---
layout: single
title: "first post"
date: 2018-01-15 15:50:41 +8000
---

post content

```

The header tells Jekyll that this file should be processd by Jekyll, and gives this file some attributes(`title` & `date`), and you can use them for example the `{{ page.title }}` inside `default.html`. The `layout` indicates which layout this post uses.

By default, you can use `post` for posts and `page` for htmls as layout without creating your own layout file.

To view your post, browse [http://username.github.io/YYYY/MM/DD/name-of-yout-post](http://username.github.io/YYYY/MM/DD/name-of-yout-post).

{% raw %}
**Note**: if you want Jekyll to ignore texts like `{{ this }}`, there are two ways:

* replace `{{ this }}` with {{ "{{ this " }}}}
* replace `{% this %}` with {{ "{% this " }}%}
* place target texts inside `{% raw %} ... {{" {% endraw " }}%}`
{% endraw %}

## Index

When you browse [https://username.github.io](https://username.github.io), you actually browse `index.html` at the repository's root.

Here is a good `index.html` example that lists all your posts with title and date:

{% raw %}
```html
---
layout: page
title: index
---

<h2>{{ page.title }}</h2>

<p>test</p>

<ul>
    {% for post in site.posts %}
        <li>{{ post.date | date_to_string }} <a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
</ul>
```
{% endraw %}

See [Jekyll/variables](jekyllcn.com/docs/variables) for more detail about the variables used here.

## More customize

By editing the `_config.yml` file, 