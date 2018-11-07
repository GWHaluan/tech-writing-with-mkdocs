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


### Code Highlighting


### ToC Permalinks


## Deployment

### Pipeline

### github pages

## 
