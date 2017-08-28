JEKYLL QUICK REFERENCE (CHEAT SHEET)
COMMANDS
Jekyll
build or b
serve or s
new
docs
doctor
Quickstart:

$ jekyll new my-site
$ cd my-site
$ jekyll build
$ jekyll serve    # => Browse your site; open the page @ http://localhost:4000
FOLDER STRUCTURE
Minimial:

├── _config.yml                        # site configuration
├── _posts                             # blog posts
|   ├── 2015-01-01-week-1-factbook.md  #   filename format => YEAR-MONTH-DAY-TITLE.MARKUP
|   ├── 2015-01-08-week-2-hoe.md
|   └── 2015-01-15-week-3-slideshow.md
├── _layouts                           
|   ├── default.html                   # master layout template
|   └── post.html                      # blog post template
├── feed.xml                           # web feed template (e.g. in rss or atom format)
└── index.html                         # index template
will result in:

└── _site                                  # output build folder; site gets generated here
    ├── 2015
    |   └── 01
    |       ├── 01
    |       |   └── week-1-factbook.html   # blog post page
    |       ├── 08
    |       |   └── week-2-hoe.html        # another blog post page
    |       └── 15
    |           └── week-3-slideshow.html  # another blog post page
    ├── feed.xml                           # web feed (e.g. in rss or atom format)
    └── index.html                         # index page
With post drafts, page collections, data stores and shared building blocks:
```

├── _config.yml                        # site configuration
├── _posts                             # blog posts
|   ├── 2015-01-01-week-1-factbook.md  #  filename format => YEAR-MONTH-DAY-TITLE.MARKUP
|   ├── 2015-01-08-week-2-hoe.md
|   └── 2015-01-15-week-3-slideshow.md
├── _drafts                            # upcoming posts; not yet published 
|   ├── week-4-kramdown.md             # note: no date required 
|   └── week-5-feedparser.md
├── _layouts                           
|   ├── default.html                   # master layout templates
|   ├── book.html                      # book listing template
|   └── post.html                      # blog post template
├── _includes                          # shared building blocks
|   ├── footer.html
|   └── header.html
├── _data                              # data store (formats available .yml, .json, .csv)
|   └── members.csv
├── _books                             # page collection (for books)
|   ├── ruby-under-a-microscope.md
|   └── learn-ruby-the-hard-way.md
├── books                               
|   └── index.html                     # book listing index template
├── members.html                       # member listing template
├── feed.xml                           # web feed template (e.g. in rss or atom format)
└── index.html                         # site index 
```

template
Note: The _post, _drafts, _layouts, _includes, _data, _books, _site folders must start with an underscore (_).

_POSTS FOLDER
The post file name must follow the format: YEAR-MONTH-DAY-TITLE.MARKUP (e.g. 2015-01-15-week-3-slideshow.md). The permalinks can be customized for each post, but the date and markup language are determined by the file name.
```
├── _posts             
|   ├── 2015-01-01-week-1-factbook.md
|   ├── 2015-01-08-week-2-hoe.md
|   └── 2015-01-15-week-3-slideshow.md
```

Front Matter
```
---
layout: post
title:  "Week #3 - slideshow gem - a free web alternative to PowerPoint and Keynote in Ruby"
---
```

Excerpt

```
---
excerpt_separator: <!--more-->
---
```
Excerpt
```
<!--more-->
```

Out-of-excerpt
TIPS & TRICKS
Including images and resources

![Ruby under a Microscope Book Cover]({{site.url}}/i/book-ruby-under-a-microscope.png)

[Hoe PDF Booklet](http://docs.seattlerb.org/hoe/Hoe.pdf); 6 Pages
_DRAFT FOLDER
Drafts are unpublished posts without a date.

```
├── _drafts
|   ├── week-4-kramdown.md
|   └── week-5-feedparser.md

```
_LAYOUTS FOLDER
TBD

_INCLUDES FOLDER
TBD

_DATA FOLDER
TBD

_COLLECTION_ FOLDER E.G _BOOKS, _ALBUMS, ETC.
TBD

GLOBAL VARIABLES
site             -- Sitewide information + configuration settings from  _config.yml.
page             -- Page specific information + the front matter.
                    Custom variables set via the front matter will be available here.
content          -- In layout files, the rendered content of the Post or Page being wrapped. 
                    Not defined in Post or Page files.
paginator        -- When the paginate configuration option is set, this variable becomes available for use.
SITE VARIABLES (BUILT-IN)
site.time           --  The current time (when you run the jekyll command)
site.pages          --  A list of all Pages
site.posts          --  A reverse chronological list of all Posts
site.related_posts  --  If the page processed is a Post, this contains a list of up to ten related Posts.
                        By default, these are low quality but fast to compute.
                        For high quality but slow to compute results, run the
                        jekyll command with the --lsi (latent semantic indexing) option.
site.static_files   --  A list of all static files (i.e. files not processed by Jekyll's converters or
                        the Liquid renderer). Each file has three properties: path, modified_time and extname
site.html_pages     --  A list of all HTML Pages.
site.collections    --  A list of all the collections.
site.data           --  A list containing the data loaded from the files located in the _data folder.
site.documents      --  A list of all the documents in every collection.

site.categories.CATEGORY   -- The list of all Posts in category CATEGORY.
site.tags.TAG              -- The list of all Posts with tag TAG.
SITE VARIABLES (CUSTOM)
All variables set via the command line and in your _config.yml site configuration are available through the site variable. For example, if you have url: http://openfootball.github.io in your configuration file, then in your Posts and Pages it will be stored in site.url.

If you add in your _config.yml site configuration, for example:

url:   'http://openfootball.github.io'
title: 'football.db - Open Football Data'
than you can use the variables in your posts, pages and templates:

site.url     -- your site's url
site.title   -- your site's title
Note: Jekyll does not parse changes to _config.yml in watch mode, you must restart Jekyll to see changes to variables.

PAGE VARIABLES
page.content      --  The content of the Page, rendered or un-rendered depending upon what
                      Liquid is being processedand what page is.
page.title        --  The title of the Page.
page.excerpt      --  The un-rendered excerpt of the Page.
page.url          --  The URL of the Post without the domain, but with a leading slash, 
                      e.g. /2015/01/15/week-3-slideshow.html
page.date         --  The Date assigned to the Post. This can be overridden in a Post's front matter
                      by specifying a new date/time in the format YYYY-MM-DD HH:MM:SS (assuming UTC),
                      or YYYY-MM-DD HH:MM:SS +/-TTTT
                      (to specify a time zone using an offset from UTC. e.g. 2015-01-15 11:11:00 +0900).
page.id           --  An identifier unique to the Post (useful in feeds). 
                      e.g. /2015/01/15/week-3-slideshow
page.categories   --  The list of categories to which this post belongs.
                      Categories are derived from the directory structure above the _posts directory.
                      For example, a post at /ruby/gems/_posts/2015-01-15-week-3-slideshow.md would have
                      this field set to ['ruby', 'gem']. These can also be specified in the front matter.
page.tags         --  The list of tags to which this post belongs. These can be specified in the front matter.
page.path         --  The path to the raw post or page. Example usage: Linking back to the page or
                      post's source on GitHub. This can be overridden in the front matter.
page.next         --  The next post relative to the position of the current post in site.posts.
                      Returns nil for the last entry.
page.previous     --  The previous post relative to the position of the current post in site.posts.
                      Returns nil for the first entry.
LIQUID TEMPLATE FILTERS N TAGS
Standard Filters
String Filters

{{ | capitalize }} -- capitalize words in the input sentence {{ | downcase }} -- convert an input string to lowercase {{ | upcase }} -- convert an input string to uppercase {{ | strip_html }} -- strip html from string {{ | strip_newlines }} -- strip all newlines (\n) from string {{ | newline_to_br }} -- replace each newline (\n) with html break {{ 'foofoo' | replace:'foo','bar' }} -- replace each occurrence # => 'barbar' {{ 'barbar' | replace_first:'bar','foo' }} -- replace the first occurrence # => 'foobar' {{ 'foobarfoobar' | remove:'foo' }} -- remove each occurrence # => 'barbar' {{ 'barbar' | remove_first:'bar' }} -- remove the first occurrence # => 'bar' {{ 'foobarfoobar' | truncate: 5, '.' }} -- truncate a string down to x characters. # => 'foob.' It also accepts a second parameter that will append to the string {{ | truncatewords }} -- truncate a string down to x words {{ 'bar' | prepend:'foo' }} -- prepend a string # => 'foobar' {{ 'foo' | append:'bar' }} -- append a string # => 'foobar' {{ "a~b" | split:"~" }} -- split a string on a matching pattern # => ['a','b'] {{ "hello" | slice: -3, 3 }} -- slice a string. Takes an offset and length # => llo

Date Filters

{{ | date }} -- reformat a date

Array Filters

{{ | first }} -- get the first element of the passed in array {{ | last }} -- get the last element of the passed in array {{ | join }} -- join elements of the array with certain character between them {{ | sort }} -- sort elements of the array {{ | map }} -- map/collect an array on a given property {{ | size }} -- return the size of an array or string

Numbers, Math Filters

``` {{ 4 | minus:2 }} #=> 2 – subtraction {{ 1 | plus:1 }} #=> 2 – addition {{ 5 | times:4 }} #=> 20 – multiplication {{ 10 | divided_by:2 }} #=> 5 – division {{ 3 | modulo:2 }} #=> 1 – remainder

{{ ‘1’ | plus:’1’ }} #=>’11’ – note: make sure to use numbers not strings ```

Escape Filters

{{ | escape }} -- escape a string {{ | escape_once }} -- returns an escaped version of html without affecting existing escaped entities

Jekyll Filters
Date, Time Filters

``` {{ site.time | date_to_rfc822 }} – Convert date to RFC-822 format # => Mon, 07 Nov 2008 13:07:54 -0800 (e.g. used in rss feeds) {{ site.time | date_to_xmlschema }} – Convert date to XML Schema (ISO 8601) format # => 2008-11-07T13:07:54-08:00 (e.g. used in atom feeds)

{{ site.time | date_to_string }} – Convert date to short format # => 07 Nov 2008 {{ site.time | date_to_long_string }} – Convert date to long format # => 07 November 2008 ```

Where, Group By, Sort Filters

{{ site.members | where:"graduation_year","2014" }} -- Select all the objects in an array where the key has the given value. {{ site.members | group_by:"graduation_year" }} -- Group an array's items by a given property e.g. [{"name"=>"2013", "items"=>[...]}, {"name"=>"2014", "items"=>[...]}] {{ page.tags | sort }} -- Sort an array {{ site.posts | sort: 'author' }} -- Optional args for hashes: {{ site.pages | sort: 'title', 'last' }} 1. property name 2. nils order (first or last)

Escape (XML, CGI, URI) Filters

{{ page.content | xml_escape }}      -- Escape some text for use in XML
{{ "foo,bar;baz?" | cgi_escape }}    -- CGI escape a string for use in a URL;
 # => foo%2Cbar%3Bbaz%3F                replaces any special characters with appropriate %XX replacements
{{ "foo, bar \baz?" | uri_escape }}  -- URI escape a string
 # => foo,%20bar%20%5Cbaz?
Convert (markdownify, slugify, sassify, jsonify) Filters

{{ page.excerpt | markdownify }}        -- Convert a Markdown-formatted string into HTML
{{ site.data.projects | jsonify }}      -- Convert Hash or Array to JSON
{{ some_scss | scssify }}               -- Convert a SCSS-formatted string into CSS
{{ some_sass | sassify }}               -- Convert a Sass-formatted string into CSS

{{ "The _config.yml file" | slugify }}            -- Convert a string into a lowercase URL "slug";
 # => the-config-yml-file                            with option 'pretty': spaces and non-alphanumeric chars
{{ "The _config.yml file" | slugify: 'pretty' }}     except for ._~!$&'()+,;=@
 # => the-_config.yml-file                           
Misc Filters

{{ page.content | number_of_words }}         -- Count the number of words in some text
 # => 1337
{{ page.tags | array_to_sentence_string }}   -- Convert an array into a sentence. Useful for listing tags
 # => foo, bar, and baz
Jekyll Tags
Include Tag

{% include footer.html %}                  -- Searches for include file in _includes folder
{% include footer.html param="value" %}       You can also pass parameters to an include

{% include_relative somedir/footer.html %} -- Searches for include file relative to the file where used
Code Syntax Highlighting Tag

{% highlight ruby %}
def main
  puts 'Hello World'
end
{% endhighlight %}
{% highlight ruby linenos %}         -- Use line numbers
def main
  puts 'Hello World'
end
{% endhighlight %}
Gist Tag

{% gist hyde/931c1c8d465a04042403 %}
{% gist hyde/931c1c8d465a04042403 hello_world.rb %}  -- You may specify the filename to display
(Source: see jekyll-gist gem)

PERMALINKS
For example, the default date permalink is defined as:

/:categories/:year/:month/:day/:title.html
Template variables
year        -- Year from the Post’s filename
month       -- Month from the Post’s filename
i_month     -- Month from the Post’s filename without leading zeros.
day         -- Day from the Post’s filename
i_day       -- Day from the Post’s filename without leading zeros.
short_year  -- Year from the Post’s filename without the century.
title       -- Title from the Post’s filename
categories  -- The specified categories for this Post.
               If a post has multiple categories, Jekyll will create a hierarchy
               (e.g. /category1/category2). Also Jekyll automatically parses out double slashes in the URLs,
               so if no categories are present, it will ignore this.
Built-in permalink styles
date      /:categories/:year/:month/:day/:title.html
pretty    /:categories/:year/:month/:day/:title/
none      /:categories/:title.html
Permalink style examples
Given a post named: /2015-01-15-week-3-slideshow.md

None specified (date)             /2015/01/15/week-3-slideshow.html
pretty                            /2015/01/15/week-3-slideshow/index.html
/:month-:day-:year/:title.html    /01-15-2015/week-3-slideshow.html
/blog/:year/:month/:day/:title    /blog/2015/01/15/week-3-slideshow/index.html
TEMPLATE EXAMPLES
Displaying an index of posts
```html

{% for post in site.posts %}
{{ post.title }}
{% endfor %}
```

Displaying an index of posts with excerpts
```html

{% for post in site.posts %}
{{ post.title }} {{ post.excerpt }}
{% endfor %}
```

Paginator
paginator.per_page            --  Number of Posts per page
paginator.posts               --  Posts available for that page
paginator.total_posts         --  Total number of Posts
paginator.total_pages         --  Total number of Pages
paginator.page                --  The number of the current page
paginator.previous_page       --  The number of the previous page
paginator.previous_page_path  --  The path to the previous page
paginator.next_page           --  The number of the next page
paginator.next_page_path      --  The path to the next page
Render the paginated Posts

```html {% for post in paginator.posts %}

{{ POST.TITLE }}
{{ post.date }}

{{ post.content }}
{% endfor %}

{% if paginator.previous_page %} Previous {% else %} Previous {% endif %} Page: {{ paginator.page }} of {{ paginator.total_pages }} {% if paginator.next_page %} Next {% else %} Next {% endif %}
```

https://github.com/feedreader/planet-jekyll/blob/gh-pages/QUICKREF.md