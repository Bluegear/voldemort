## Voldemort

Voldemort is a simple static site generator using Jinja2 and markdown templates.

## Installation

    sudo python setup.py install

## Usage Options

    Usage: voldemort [options]
    
	Options:
	  -h, --help            show this help message and exit
	  -w WORK_DIR, --work_dir=WORK_DIR
	                        Working Directory
	  -s, --serve           Start the HTTP Server
	  -p PORT, --port=PORT  Port inwhich the HTTPServer should run
	  -d, --deploy          Deploy this website
	  -u USER, --user=USER  Login name for server
	  -a AT, --at=AT        Server address to deploy the site
	  -t TO, --to=TO        Deployment directory

## Usage Example

Go to the example directory

	cd example

and run

	voldemort

start the HTTPServer

	voldemort --serve --port 8080

Open your browser and see the website in action.

Deploy the website

	voldemort --deploy --user foobarnb --at foobarnbaz.com --to ~/public_html

## Writing posts

Posts mainly contain 2 sections. Config section and the Template section. All data inside two `---` defines the config and are validated as YAML data. You can set your post related attributes here. In template section you can use Jinja2 templates or Markdown in `{% markdown %}` and `{% endmarkdown %}` blocks.

As per Voldemort's default configuration, all base templates should be in `layout` and `include` directories. This is not a hard limitation, but kept for preserving the meaning. Posts are written in a directory named `posts`. For example, we have a directory structure as shown below

	layout/
		base.html
		post.html
	include/
		navigation.html
	posts/
		voldemort-is-awesome.markdown
	index.html
	css/
		screen.css
		pygments.css

And we have the following data in `layout/base.html`

	<!DOCTYPE html>
	<html lang="en-US">

	<head>
	<title>foobarnbaz.com - {{ page.title }}</title>
	{% include "head-common.html" %}
	</head>

	<body>
	<section class="page-content">
	{% block content %}{% endblock %}
	</section>
	</body>
	</html>

and `include/header.tml` contains

	<meta charset="UTF-8" />
	<meta http-equiv="content-type" content="text/html; charset=utf-8" />
	<meta name="author" content="Sreejith K" />

	<link rel="alternate" href="http://feeds2.feedburner.com/foobarnbaz"
	  title="foobarnbaz.com" type="application/atom+xml" />
	<link rel="stylesheet" href="/css/screen.css" type="text/css" />
	<link rel="stylesheet" href="/css/pygments.css" type="text/css" />
	<link href='/images/layout/favicon.ico' rel='shortcut icon' type='image/ico' />

We will be able to write the following `index.html` which generates the front page of your blog with all the posts, paginated with the value provided in `settings.yaml` (defaults to 5).

	---
	paginate: true
	---
	{% extends "listing.html" %}
	{% block content %}

	{% for post in paginator.posts %}
	<article class="excerpt">
	<header>
	<h1><a href="{{ post.url }}">{{ post.title }}</a></h1>
	<time datetime="{{ post.date.strftime("%Y-%m-%d") }}" pubdate="pubdate">
	{{ post.date.strftime("%b %d, %Y") }}
	</time>
	</header>

	{% if loop.first %}
	{{ post.content }}
	<p class="full-post"><a href="{{ post.url }}#comments">comments...</a></p>
	{% else %}
	<p>{{ post.content }}</p>
	<p class="full-post"><a href="{{ post.url }}">full post...</a></p>
	{% endif %}

	</article>
	{% endfor %}
	{% endblock %}

And our sample post `posts/voldemort-is-awesome.markdown`,

	---
	title: Voldemort
	date: '02-10-2011'
	time: '10:45'
	---
	{% extends "post.html" %}

	{% block postcontent %}
	{% markdown %}

	[Voldemort](https://github.com/semk/voldemort) is an awesome static site generator based in Jinja2 and Markdown templates.

	{% endmarkdown %}
	{% endblock %}

For more information about templating read the following documentations.

* Read [Jinja2 Documentation](http://jinja.pocoo.org/docs/templates/)
* Read [Markdown Documentation](http://daringfireball.net/projects/markdown/syntax)

## Configuration

You can change the default settings by editing the `settings.yaml`.

	layout_dir  : layout		# directory inwhich base tempaltes reside
	include_dir : include		# html code that can be included goes here
	posts_dir   : posts			# directory where you write posts
	post_url    : "%Y/%m/%d"	# url to posts. You can alter the order
	site_dir    : _site			# generated site will be in this directory
	paginate    : 5				# number of pages to be paginated at once

## Global variables

	posts:		A list of all your posts. All attributes in the YAML section 
				can be accessed either using . or []. 
				eg. post['date'], post.date
	
	paginator:	You can paginate your posts using this object.
				eg: {% for post in paginator.posts %}
				Attributes:
					posts:	list of posts in this paginator
					current_page	: current page number (None if not)
					next_page		: next page number (None if not)
					previous_page	: previous page number (None if not)
	
	post:		Info about the post. Only accessible in posts.
				Attributes:
					content			: html content of the post
					url				: url to this post
					next			: points to the next post
					previous		: points to the previous post
				and you can access all the attributes in the config section (eg: post.date)
	
	page:		Info about a page. Only available in pages other than posts.
				Attributes:
					content			: html content of the post
				and you can access all the attributes in the config section (eg: page.title)

## Developer

Sreejith K <sreejithemk@gmail.com>

http://foobarnbaz.com
