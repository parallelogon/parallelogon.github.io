---
layout: single
title: Getting Up and Running
published: true
date: 2021-10-11
use_math: true
categories:
  - blog
tags:
  - Jekyll
  - update
---

This first post is about the tips and tricks I've used in order to get this github blog/repo up and running.  It is written primarily as a reminder to myself about how to go about building something similar and as possible instructions for whichever desperate soul comes across the same struggles.  The work that I've done can be grouped into three sections:

1. Installing minimal mistakes and getting the site running
2. Enabling Mathjax support
3. Building the resume section

While these are all fairly small things, the newness of mixing Jekyll, markdown, and HTML lead to some tricky cases that can be nontrivial to solve for the uninitiated (myself)
# Setting Up The Basic Site
After setting up a github repo using [a Minimal Mistakes starter][minimal-mistakes-starter].  The link included automatically sets up a repository with the most basic possible version of a github website/template.  The rest is just customization.  I found it really helpful to also clone a full copy of the minimal mistakes template into a seperate directory in order to compare files as the one linked only gives basic information.  It's fairly straightforward to fill in the desired information, and a small example of the type of thing you may include is given below:
~~~yaml
title: ZJ
email: z.a.c.h.a.r.y.j.o.n.e.s@outlook.com
description: >- # this means to ignore newlines until "baseurl:"
	A homepage and digital resume for Zachary Jones
~~~
Note the syntax of `attribute: value`{:.language-yaml} where there is an attribute name followed by a colon, then space, then value.  Look at the full _config.yml file in the [main minimal mistakes repo][minimal-mistakes].  My completed _config.yml can be seen [here][my-yml].  Notice the line `markdown: kramdown`{:.language-yaml}.  Jekyll uses a modified version of markdown which allows for the user to mix in HTML and CSS.  It is worthwhile to get familiar with its uses early as all posts ultimately are written using this syntax, a cheatsheet and basic explanation can be found [here][kramdown].

Now that the basic page is running (you should check that this is true) one can start to modify the page.  It's fairly plain to see, however, that the modifications are slow to be added to the github site as they need to be processed and implemented according to some type of scheduled process.  Instead of labourously making small changes, pushing to the main repository, and waiting to see if they are successful it is also worthwhile to view a local copy of your site before commiting changes.  This can be done in a straightforward manner by simply following the [instructions given by github itself][jekyll-local].

## Making a Post
Before making the first post, one can first look through the ```_posts```{:.language-yaml} directory in order to get a feel for what to do.  Posts are saved as ```yyyy-mm-dd-the-title-seperated-by-dashes.md```{:.language-markdown} Notice the first section of every post begins with three dashed lines as so:
~~~yaml
---
title: "Welcome to Jekyll!"
published: true
date: 2019-04-18T15:34:30-04:00
categories:
  - blog
tags:
  - Jekyll
  - update
---
~~~
This is YAML (YAML ain't Markup Language) frontmatter, to see all of the possible `tag: value` combinations look through the example posts given as there are quite a few possibilities.  To make the default posts invisible, go through each file and change the `published` key to `false`.


Another small change is to the default code highlighting.  In order to customize the syntax highlighting I 
1. copied the `main.scss` file from the full minimal mistakes installation (located in `/assets/css`) into my own (self made) `/assets/css` directory
2. modified the CSS using the [pygments][pygments-repo] by copy-pasting the `default.css` CSS into `/assets/css/default.css` 
3. append `@import "default.css"` to the newly copied `main.scss` file in my directory

One can then adjust background color or other attributes according to taste.
{% endcomment %}
# Mathjax support
Not only do I want to write code, but I want to be able to display math rendered in mathjax:


$$
\begin{align} 
	k(x,y) & = \int_{\mathbb{R}^d} p(\omega) e^{i\omega t} d\omega \nonumber \\
	& = \mathbb{E}\big[e^{i\omega t} \big]
	
\label{eq: kernel}
\end{align}
$$

Beautiful!  The equation can even be referenced \ref{eq: kernel} using `\ref{label}` just as in Latex.  Enabling mathjax takes two steps:
1. create a file in the `_includes` directory: `_includes/mathjax_support.html` and paste into it the following code [source][jekyll-problem-so]:

        ~~~html
        <script type="text/x-mathjax-config">
          MathJax.Hub.Config({
            TeX: {
              equationNumbers: { autoNumber: "AMS" },
              tagSide: "right"
            }
        });
        </script>
        <script type="text/javascript" charset="utf-8"
          src="https://cdn.jsdelivr.net/npm/mathjax@2/MathJax.js?config=TeX-AMS_CHTML">
        </script>
        ~~~
This sources mathjax (the bottom part) and configures automatic numbering for the `\begin{equation}...\end{equation}` and `\begin{align}...\end{align}` functions work as expected.

2. From the original minimal mistakes page copy `_layouts/default.html` into the project's own `_layouts` directory and add the following code before the `</head>` tag:

{%raw%}
~~~
{% if page.use_math %}
	{% include mathjax_support.html %}
{% endif %}
~~~
{%endraw%}

Afterwards, add `use_math: true` to the YAML front matter of any given post and enclose standard latex math expressions in `$$` delimiters!

## Building a Resume
To navigate different parts of the website, Jekyll looks in the `_pages` directory in order to source pages with the correct peramalink in the YAML header.  Adding a new section (in this case for a digital version of my resume) again boils down to two steps:

1. Add a new link and permalink to the `_data/navigation.html` file, mine looks like this:
        ~~~yaml
        main:
          - title: "Posts"
            url: /posts/
          - title: "Categories"
            url: /categories/
          - title: "Tags"
            url: /tags/
          - title: "About"
            url: /about/
          - title: "Resume"
            url: /resume/
        ~~~

2. In the `_pages` directory add a file `resume.md` with the following YAML front matter
~~~yaml
---
permalink: /resume/
title: "Resume"
---
~~~

Then the only thing left to do is to write the resume in standard markdown!  I used [working with caroline][c-s] for inspiration.  One trick not covered there was how to embed a 'two column' type environment, which I wanted to do in order to include dates along with working locations.  The easiest way I found to do this was to embed my markdown code in a paragraph environment as so:
~~~html
<div>
<p style = "float: left" markdown = "1">Left hand stuff goes here</p>
<p style = "float: right" markdown = "1">right hand stuff goes here</p>
</div>
<div styel = "clear: both;"></div>
~~~
Where the ending `<div styel = "clear: both;"></div>` resets the environment.  All that was left to do was then write the rest of the resume.


[c-s]: https://github.com/carolstran/cv/blob/main/README.md
[jekyll-problem-so]: https://stackoverflow.com/questions/59141529/mathjax-equation-numbers-do-not-show-using-jekyll-on-github-pages
[pygments-repo]: https://richleland.github.io/pygments-css/
[jekyll-local]: https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/testing-your-github-pages-site-locally-with-jekyll
[kramdown]: https://kramdown.gettalong.org/syntax.html
[my-yml]: https://github.com/parallelogon/parallelogon.github.io/blob/0b741509330773ecfe32048a72e790fe4fe35336/_config.yml
[minimal-mistakes]: https://github.com/mmistakes
[minimal-mistakes-starter]: https://github.com/mmistakes/mm-github-pages-starter
