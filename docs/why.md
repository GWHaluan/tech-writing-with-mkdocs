# Why mkdocs?

There are many ways to organize your Technical Writing.
What it comes down to, is your specific use case.
When i started my last job, the situation was this:

* We wanted to provide useful user documentation
* There were many Word documents that were or were not up to date
* Confluence and Scroll Viewport were proposed as a solution
* Some effort had been done to implement this, mostly text conversion from Word to Confluence

We decided against Confluence and used Markdown and mkdocs instead.
Our reasons were:

* Confluence and most of its plugins are proprietary and WYSIWYG
* For business reasons, we did not have direct control over Scroll Viewport styling
* There was no link between Confluence and the software release process
* We saw a major gap to our software development as developers disliked using Confluence
* Pipeline and deployment tools were already in use in the development teams
* Markdown is simple and therefore easily customizable and portable

## Features

### Plain Text Source

Markdown is an easy to pick up simple markup language and thus in plain text format.
Writing texts in plain text format is an advantage because you store and maintain information on how it is displayed separately.

### Theming and Styling via mkdocs

mkdocs has a number of more or less well-designed themes.
You can alter them to your liking.
Please refer to my recommended [mkdocs setup](config.md) for more information.

### Easy Collaboration

Due to its plain text nature, you can easily work with a version-control software such as git.
Collaboration and reviewing are quite easy that way, and comparable to the maintenance of software code.
Checkout [docs as code](http://www.writethedocs.org/guide/docs-as-code/).

## Problems

### Static Explicit Navigation

Your mkdocs navigation is maintained in the `mkdocs.yml` file.
For complete control, you need to reference every Markdown file that you want to be part of your documentation.
You can have Markdown infer the navigation title from the `h1` headline of a Markdown file, or override it in your `mkdocs.yml` file:

``` yaml
nav:
  - Home: index.md
  - config.md
```

In this example, the navigation entry *Home* is explicit in the `mkdocs.yml` file, while the navigation entry for the configuration article is inferred from the `h1` headline in the file `config.md`.
There are some problems with such inferences and the state of nested navigation siblings, see [mkdocs issue #1347](https://github.com/mkdocs/mkdocs/issues/1347).

In my solution sketched out in the article about [distributed navigation](nav.md), there is a built-in solution for this problem as well.

### Encoding

*TODO*

### Software Variants and Versions

With a comprehensive mkdocs setup, you are likely using a number of software packages that can all have different versions and that might clash with eachother.
I cannot always recommend keeping everything up to date.
For example, 

### Multilanguage Support

### Large Search Index in Large Projects
