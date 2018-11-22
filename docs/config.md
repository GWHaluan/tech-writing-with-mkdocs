# Setup and Configuration

## git

[Install git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

## mkdocs

Install [mkdocs](https://www.mkdocs.org/).

mkdocs is written in Python. First, [install Python](https://www.python.org/downloads/), then download [`get-pip`](https://bootstrap.pypa.io/get-pip.py).

Execute the following command in the directory with `get-pip`:

    python get-pip.py

Afterwards, install mkdocs via pip:

    pip install mkdocs

In Ubuntu Linux, you need to take extra steps to install the latest versions of python and pip.
See [this tutorial](https://tecadmin.net/install-python-3-7-on-ubuntu-linuxmint/) for an installation of Python 3.7.
A Stack Overflow post provides some [additional tricks](https://serverfault.com/questions/918335/best-way-to-run-python-3-7-on-ubuntu-16-04-which-comes-with-python-3-5).

It is best to keep a `3.7`-suffix for pip, so the references are unambiguous:

``` console
user@machine:~/Desktop$ python --version
Python 2.7.15rc1
user@machine:~/Desktop$ python3.7 --version
Python 3.7.0
user@machine:~/Desktop$ pip --version
pip 18.0 from /usr/local/lib/python3.7/site-packages/pip (python 3.7)
user@machine:~/Desktop$ pip3.7 --version
pip 18.0 from /usr/local/lib/python3.7/site-packages/pip (python 3.7)
```

In this case, pip is only installed in Python 3.7.

### Theme

There are a couple of [themes](https://github.com/mkdocs/mkdocs/wiki/MkDocs-Themes) for mkdocs.

I prefer [Material](https://github.com/squidfunk/mkdocs-material), as it offers the most polished look and feel.

Install it via pip:

    pip install mkdocs-material

Activate the Material theme via your `mkdocs.yml` file:

``` yaml
theme:
  name: 'material'
```

!!! note
    Material overrides the built-in search bar, which has been greatly improved with regard to performance in the [1.0.0 Milestone release](https://www.mkdocs.org/about/release-notes/#version-10-2018-08-03) of mkdocs.
    In very large projects, this can cause the frontend to freeze for a several seconds which is unacceptable UX. <!--- Link zu Search Index Problem Abschnitt --> 

### Styling

While your [theme](#theme) dictates a general look and feel, you can style your documentation page in a number of ways.

Include css files via your `mkdocs.yml` file:

``` yaml
extra_css:
    - 'stylesheets/css.css'
```

Set a favicon and logo via your theme in your `mkdocs.yml` file:

``` yaml
theme:
  name: 'material'
  logo: 'favicon.ico'
  favicon: 'favicon.ico'
```

### Comments

Sadly, Markdown does not provide a syntax for comments.
There is a somewhat dirty workaround with comments in link syntax with empty link text, for example:

    [](This comment cannot be seen because there is no link text)

You can also just use html comments:

``` html
<!-- A comment -->
```

This comment will be visible in the page source when you convert to html though.

The cleanest implementation for Markdown comments is the Markdown extension `mkdcomments`.

Install it via pip:

    pip install git+https://github.com/ryneeverett/python-markdown-comments.git

Activate it via your `mkdocs.yml` file:

``` yaml
markdown_extensions:
  - mkdcomments
```

Now you can use html comments with a slight modification (three dashes instead of two), and they will not appear in the page source when you convert to html:

``` html
<!--- A comment -->
```

### Includes

Sadly, Markdown does not provide a syntax to include a partial or the entire content of other Markdown files.
Luckily, there is a Markdown extension for it: [Markdon-Include](https://github.com/cmacmackin/markdown-include).

Install it with:

    pip install git+https://github.com/ryneeverett/python-markdown-comments.git

Activate it via your `mkdocs.yml` file:

``` yaml
markdown_extensions:
  - markdown_include.include
```

Note that the `.include` suffix is necessary.

Reference other Markdown files like this:

```
!path/to/filename.md!
```

but wrapped in `{` and `}`.

Note that you can set a base URL for include paths:

``` yaml
markdown_extensions:
  - markdown_include.include:
       base_path: docs
```

This base path will be prefixed to paths you set for includes.

If there are broken links, a warning will appear on your `mkdocs build` or `mkdocs serve` console output:

``` console
WARNING -  Documentation file 'config.md' contains a link to 'deadlink.md' which is not found in the documentation files. 
```

However, you cannot configure mkdocs to fail its `build` or `serve` in case of a warning, as the use of `strict mode` would do.

The [source code](https://github.com/cmacmackin/markdown-include/blob/master/markdown_include/include.py) uses a `print` statement for the warning in the `except` block of the `run` function.

My recommendation for an error exit upon broken links would be to:

1. clone the repository
2. override the `except` block like so:


        except Exception as e:
           print('Warning: could not find file {}. Ignoring '
               'include statement. Error: {}'.format(filename, e))
           lines[loc] = INC_SYNTAX.sub('',line)
           raise e
    i.e., rather than using a `break` statement, raise the exception and cause an error.

3. install markdown locally via:

        pip install -e .
    in the directory where you modified the cloned include extension.
    You might as well host the fork, e.g. in bitbucket, and install it from there.

### Code Highlighting

There is an extension for code highlighting that works beautifully with the Material theme: [codehilite](https://python-markdown.github.io/extensions/code_hilite/).

Activate it via your `mkdocs.yml` file:

``` yaml
markdown_extensions:
  - codehilite
```

### ToC Permalinks

To enable Permalinks for your page headlines, use the `toc` extension and add the following snippet to your `mkdocs.yml` file:

``` yaml
markdown_extensions:
  - toc:
       permalink: True
```

Next to each headline, a paragraph symbol will appear upon hover, and act as an anchor URL to the section.
When you click it, you can copy the link from your browser address bar.

There is a problem with the `toc` extension:
Anchor links are generated from headlines, and specifically from the headline strings.
However, they are language-specific.
In case you want to translate your project, translating any headlines other than first level, will break any incoming links to their sections.
There is a workaround, refer to my idea for [translation post-processing](tlpostproc.md).

## Mermaid Diagrams

With [mermaid diagrams](https://mermaidjs.github.io/), you can create certain types of schematic images via a simple syntax and maintain the image source code directly within your Markdown files.

mermaid is written in Javascript, and thus you can include its script and css files in your mkdocs.yml file:

``` yaml
extra_css:
    - https://unpkg.com/mermaid@7.1.2/dist/mermaid.css
extra_javascript:
    - https://unpkg.com/mermaid@7.1.2/dist/mermaid.min.js
```

You can now embed a graph definition in a `div`-tag with class attribute of `mermaid` in your Markdown files like so:

``` xml
<div class="mermaid">
graph LR
    Start --> Stop
</div>
```

mermaidjs will convert this to a graph like so:

``` mermaid
graph LR
    Start --> Stop
```

In order to use the fenced code macro ```` mermaid` instead of html tags, you need to customize a Markdown extension called `pymdownx`. Add the following to your mkdocs.yml file:

``` yaml
markdown_extensions:
    - pymdownx.superfences:
        custom_fences:
          - name: mermaid
            class: mermaid
            format: !!python/name:pymdownx.superfences.fence_div_format
```

Check out gilbsgilbs' post [here](https://github.com/squidfunk/mkdocs-material/issues/693#issuecomment-411885426).