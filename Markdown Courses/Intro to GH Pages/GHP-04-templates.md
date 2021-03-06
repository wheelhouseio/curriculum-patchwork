
# Templates and Design

In this lesson, we'll teach you how to edit templates in Jekyll so that you can create new page types and change your current design. This lesson assumes knowledge of HTML and CSS. If you'd like to learn, there are a bunch of free resources online. We'd recommend (and will at times link to) the <a href="http://www.w3schools.com/" target="_blank">W3Schools tutorials</a>.

## HTML Templates

Jekyll uses a templating engine called Liquid. Liquid works by creating HTML templates with little snippets of code in them. When displaying, Jekyll will execute the code, then display the results along with the HTML.

For instance, this is the default blog post template included in all new Jekyll installs. It's found in the `_layouts` folder and is called `post.html`.


    ---
    layout: default
    ---
    <div class="post">

      <header class="post-header">
        <h1 class="post-title">{{ page.title }}</h1>
        <p class="post-meta">{{ page.date | date: "%b %-d, %Y" }}{% if page.author %} • {{ page.author }}{% endif %}{% if page.meta %} • {{ page.meta }}{% endif %}</p>
      </header>

      <article class="post-content">
        {{ content }}
      </article>

    </div>



Let's look at how this works. First, there is front matter just like with our markdown files. This front matter says that this template also uses another template. Templates can be embedded within each other in this way—so you might have one basic template with stuff like a header and footer, then more specific templates that fit inside that one for static pages and blog posts.

Next up comes the HTML. As usual, we have tags with classes. 

Finally, inside the `<h1>` tag, we get our first look at liquid.

All liquid commands are placed inside either a block with two curly brackets `{{ }}` or a block with a curly bracket and a percent symbol `{% %}`. The former are commands that will render text inside the HTML. The latter are just logic commands that won't output any text.

### Content in Liquid

Our first liquid command is `{{ page.title }}`. This command is telling the template to display the text found in the `title:` variable in the page's front matter. You'll see this syntax, `object.variable` using a period a lot in liquid. When you use it, you're telling liquid to look inside the object to find and return that specific variable. 

Next, inside the `<p>` tag, comes some more complicated liquid. It starts off with `{{ page.date | date: "%b %-d, %Y" }}`. Here, we're telling liquid to display the date found in the page, but also how to display it. The pipe `|` character passes the content returned by the command on the left through the process to the right. The `date:` command accepts a date from liquid, and then formats it in a specific way. Here we started with the abreviated month name `%b`, a space, then the day of the month without a leading zero `%-d`, then a comma and space, then the 4-digit year `%Y`. So `Jan 5, 2016` would be the output for the post from the last lesson.

### Logic in Liquid

After that, we have our first logic command, found inside a curly bracket with percent symbol. This is an `if` command. It tells liquid to only display the contents between the `if` and `endif` commands if the condition in the `if` command is true. Here, `if page.author` will only be true if the post has an `author:` variable defined in the front matter. For instance, if the post had Jamal listed as an author, liquid would display ` • Jamal`, while if it didn't have an author listed, liquid would not display the name or the bullet point. The same is true for the `page.meta` variable.

Finally, liquid will display the content of the post using a special keyword, `content`. Unlike with our other variables, in templates we don't need to specificy the object we're calling the content from.

There is one more logical operator you should know about. The `for` command allows you to cycle through multiple objects. For example, let's look at the following code excerpted from our blog page found in `/_pages/03_blog.md`

      <ul class="post-list">
        {% for post in site.posts %}
          <li>
            <span class="post-meta">{{ post.date | date: "%b %-d, %Y" }}</span>

            <h2>
              <a class="post-link" href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a>
            </h2>
          </li>
        {% endfor %}
      </ul>

Here, the `for` command takes every file in the `_posts` folder (called with `site.post`), creates a temporary object called `post` for that file, and then populates the content below for each of those files. 

You can combine for and if statements, for instance, consider the following code from `/_includes/header.html` which creates the nav bar at the top of the page:

    <nav class="site-nav">

      <div class="trigger">
        {% for page in site.pages %}
          {% if page.title %}
          <a class="page-link" href="{{ page.url | prepend: site.baseurl }}">{{ page.title }}</a>
          {% endif %}
        {% endfor %}
      </div>
    </nav>

This will only list the pages that have a `title:` attribute defined in the front matter. This way, you can create new pages that aren't linked in the nav bar by simply not include the `title:` in the front matter.

But maybe you want all of your pages to include a `title:` but not all of them to show up in the nav bar. How might you go about doing that? A hint: You can always add more items to the front matter.

## CSS Templates Using SASS

Jekyll uses a CSS preprocessor called Sass to generate CSS files. Sass is a lot like Liquid, but for CSS instead of HTML. 

The biggest thing that Sass lets you do is break up your CSS files into little pieces and then include them in one final large file. However, it also has some other handy features including Nesting and Variables.

You can find out about all of Sass's features at <a href="http://sass-lang.com/guide" target="_blank">the Sass website</a>, but we'll explain a few below as well.

One thing to note: Jekyll won't handle your Sass files properly unless you include some frontmatter at the top of the file. The easiest way to do this is to just include a comment, so adding something like the following to each of your files will suffice.

    ---
    # Adding front matter for Jekyll
    ---

### Importing Sass files

One way to make writing CSS easier is to separate your functions. This way you might have one file that defines what a blog post looks like, one file that defines what the front page looks like, one for your header, etc.. This way it's a lot easier to go in and find the CSS you'd like to change late.

The easiest way to do this is to include all the CSS for your app in .scss files in the `_sass` folder, and then have your `main.scss` file just act as a place to import those files.

To import a file from the `_sass` folder, just include the following rule in your main.scss file.

    @import
      "filename",
      "filename2"
    ;


### Variables and Mixins

Sass lets you set variables to use throughout your CSS files. This is particularly handy for giving values that you know you'll use a lot a simple keyword. If you look in your `/css/main.scss` file, you'll see we've defined a number of variables used through the CSS. Variables can be defined _either_ in the file they're used in, _or_ in the `main.scss` file. Variables should always come at the top of a file, and always begin with a `$` sign.

Variables are particularly handy because if you'd like to change the text font everywhere, you only need to update the value of the variable.

Mixins are sort of like variables, but for properties instead of values. This way, if you know that you'll always want to define two more more properties with the same value, you can define a mixin that will take a single value and add it to all of the properties you want. 

This is particularly useful when dealing with vendor prefixes.

For information on how and when to write mixins, look at the <a href="http://sass-lang.com/guide#topic-6" targer="_blank">official Sass user guide</a>.

### Nesting

Sass lets you nest rules inside each other, just like HTML lets you nest tags. This makes writing CSS rules a little bit easier, and will force you to group like code together.

For instance, take this code from our `/_sass/_layout.scss` file:


    .site-nav {
        float: right;
        line-height: 56px;

        .menu-icon {
            display: none;
        }

        .page-link {
            color: $text-color;
            line-height: $base-line-height;

            // Gaps between nav items, but not on the first one
            &:not(:first-child) {
                margin-left: 20px;
            }
        }
      }


Here we define rules for the `.site-nav` class as we normally would. However, instead of closing the tags at the end, then opening another rule for `.site-nav .menu-icon`, we simply open the rule for `.menu-icon` inside of the rule for `.site-nav`. This way, all of the rules related to `.site-nav` are found grouped together and in context of each other, making them much easier to write and read.

## Tying it Together

When writing templates and the CSS for them, it's important to realize that you can't add `class` and `id` attributes to markdown as you would with HTML. This is where nesting CSS attributes comes in handy.

Each template you build should wrap the entirety of the markdown conten in a `div` with a `class` set to the same name as the template. For instance, the `blog.html` template wraps the markdown content in some code that looks like this:

    <div class="blog-content">
      {{ content }}
    </div>


Now if you want to give special formatting for the headers in a blog post, you'd add the following code to your `blog.scss` file:


    .blog-content {
      h1 {
        # header 1 rules
      }
      h2 {
        # header 2 rules
      }
    }

And so on. 

## Front Matter vs. Markdown

At times, you won't have enough flexibility with markdown alone to create the designs you want, and this is where adding content to front matter can come in handy. For instance, in creating our `_index/content.md` file, we could have had you place the link to the image inside the markdown, instead of in the front matter.

However, this would have given us less flexibility in defining where the image goes on the page. By putting it in the front matter, we can embed the image outside of the "content" `div`, and thus position the image relative to the content instead of inside the content. The same goes for the `author` and `title` of a blog post.

Jekyll is very versitile in this way, but it means that you'll have to experiment with what works in order to make smart decisions about how to format your site. As a general rule though, anything that you can think of as "data" _can_ go in the front matter, and anything that's better thought of as "content" _should_ go in the markdown. Whether some "data" should go in the front matter or markdown will be determined by how you want to use it in the template.

## Try it out
### Create a New Page

Below are some projects that you can take on that will challenge your templating skills. We're leaving them more open-ended instead of giving you step-by-step instructions, so feel free to ask a mentor if you need help.

* Edit your project so that you can have pages which have a `title:` element but which don't show up in the nav bar. You'll need to come up with a system (a mentor can help) and edit the `_includes/header.html` page, as well as the front matter in all of your site pages.
* Earlier you created a Work History page. This isn't very well formatted though. Try using CSS and templates to clean up the page. If you create a Work History template you can wrap the content in a new div, and then format the elements using CSS so that formatting only applies to that page.

  You might also look into the "collections" functionality in Jekyll. This would let you create a different markdown file (and front matter data) for each job description. How might this make formatting the page easier?
