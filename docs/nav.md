# Distributed Navigation

## Concept

If you have a very large project which is rather a collection of documentation projects, you might want to split up your navigation into multiple files and thus split up your mkdocs.yml file.
However this is not an option with vanilla mkdocs.

Luckily, there is a [Plugin option for mkdocs](https://www.mkdocs.org/user-guide/plugins/#developing-plugins) for easy integration of your own code.

The yml file format is simple enough to compose a new yml file from a number of sub-yml files more or less directly.
For mkdocs, this means that you want to compose the content of the `nav`-key of your mkdocs.yml file from the `nav`s in your sub-yml files.

To simplify things, i recommend keeping all the yml files you want to compile in the same directory, i.e. in the same directory where mkdocs creates the docs subdirectory:

```
docs
 |- index.md
 |- some-other.md
 |- subdir1
     |- index.md
     |- another.md
 |- subdir2
     |- index.md
     |- yet-another.md
mkdocs.yml
ref1.yml
ref2.yml
```

It is also handy to separate the documentation projects' Markdown files in subdirectories of `docs`.

Assuming that `ref1.yml` builds a documentation project in `subdir1` and `ref2.yml` in `subdir2`, the `nav`-keys can look like this:

``` yaml
nav:
 - Home: subdir1/index.md
 - Some title: subdir1/another.md
```

``` yaml
nav:
 - Home Two: subdir2/index.md
 - Some different title: subdir2/yet-another.md
```

Reference `ref1.yml` and `ref2.yml` in your `mkdocs.yml` like so:

``` yaml
nav:
 - Space 1: ref1.yml
 - Space 2: ref2.yml
```

If you use the plugin described below, during your mkdocs build or serve this will be evaluated to:

``` yaml
nav:
 - Space 1: 
	 - Home: subdir1/index.md
	 - Some title: subdir1/another.md
 - Space 2:
	 - Home Two: subdir2/index.md
	 - Some different title: subdir2/yet-another.md
```

## Code

Define a class, and set it up as described in [plugin development for mkdocs](https://www.mkdocs.org/user-guide/plugins/#developing-plugins).

Use the `on_config` method as an entry point, look for your `nav`-key in the mkdocs.yml file, then iterate through any entries that reference another yml file. For each reference, read that yml-file, and insert its `nav`-key into the parent file.
    
``` python
def on_config(self, config, **kwargs):
    toc = config['nav']
    self.makeYML(toc)
    
def makeYML(self, toc):
    def itertoc(li):
        for i in range(len(li)):
            entry = li[i]
            if (type(entry) is str):
                if (entry.endswith(".md")):
                    p = os.path.join(cwd, "docs", os.path.normpath(entry))
                    # value points to a file
                    if (os.path.exists(p) and os.path.isfile(p)):
                        tocEntry = {}
                        for line in open(p):
                            matchwhite = re.match('[^\S\n]+', line)
                            match = re.match('#([^#].*)', line)
                            if matchwhite:
                                continue
                            elif match:
                                tit = match.group(1).strip()
                                tocEntry[tit] = entry
                                break
                            else:
                                head, tail = os.path.split(p)
                                self.logger.debug("this filename: " + tail)
                                break
                        if (tocEntry == {}):
                            fnam = os.path.split(entry)[1].split('.')[0]
                            tocEntry[fnam] = entry
                        li[i] = tocEntry
            else:
                for k, v in entry.items():
                    # case value is a list
                    if (isinstance(v, type([]))):
                        entry[k] = itertoc(v)
                    else:
                        # value is a string
                        p = os.path.join(cwd, v)
                        # value points to a file
                        if (os.path.exists(p) and os.path.isfile(p)):
                            ext = v.split('.')[-1]
                            # case value points to a .yml file: load 'nav'-value.
                            if (ext == ymlExtension):
                                with open(p, 'r') as stream:
                                    data = yaml.load(stream)
                                    subtoc = data['nav'] 
                                    entry[k] = (itertoc(subtoc))
        return li
    itertoc(toc)
    return
```

I omitted a couple of imports here, notably `mkdocs`, `yaml`, `sys`, `os` and `re`.