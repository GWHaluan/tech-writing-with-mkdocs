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
I cannot recommend keeping everything up to date.
For example, the Markdown software is incompatible with `markdown_include` since the [3.0 release of Markdown](https://libraries.io/pypi/Markdown/3.0).
In order to keep things compatible, you need to deliberately use version 2.6.11.

### Multi-Language Support

There is no multi-language support for mkdocs currently.
Possible solutions and discussions about them are scarce and mostly open issues of the project (eg [issue #614](https://github.com/mkdocs/mkdocs/issues/617)).

For a translation workflow you would need to either translate Markdown files and yml files or the mkdocs-generated HTML files.
The Markdown format can be problematic.
In order to have a clean translation, language-specific hosting could work, i.e. to have a sub-project per language that is structurally equivalent to what you have translated.

### Large Search Index in Large Projects

The built-in mkdocs search is problematic to say the least.
It uses lunr.js and parses all documentation pages into a search index.
This index is then queried when a user enters a search term into the search bar.
The way it was implemented up to [milestone release 1.0](https://www.mkdocs.org/about/release-notes/#version-10-2018-08-03), the search index was parsed every time the user entered a search term on a specific page.
This means, whenever the user navigated to a different page and then used the search bar again, unnecessary index parsing occured.
This is still the case if you use the Material theme for mkdocs, as it overrides the improved search that was released in 1.0.

For a larger documentation project (we had around 1200 pages), the search index is substantially large (60mb+) to slow down parsing a lot.
This meant that the search bar was unusable, since it blocked the GUI for several seconds upon use.

If Material didn't override the search, it would be usable by now. However, I recommend not using the Javascript search at all but instead implement your own server-side search.