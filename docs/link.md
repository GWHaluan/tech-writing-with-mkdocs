# Link Checking

mkdocs does not come with integrated link checking.
In a living documentation, file names as well as section titles are going to change on a regular basis.
Every time that happens, any links to those files or section titles inevitably break and need to be fixed.
Thus it would be nice to have an automated process that detects these broken links so you do not have to search for them yourself.

The solution sketched below uses a recursive wget command that can be run during a build process.

## wget

GNU Wget is a program that retrieves web content.
It can be utilized as a link checker because it will return a 404 if it cannot follow links when invoked recursively.

Take for example this [broken link](non-existing.md) (relative Markdown link to `non-existing.md`).

Now, if you invoke wget like so:

    wget --recursive --no-verbose --spider -e robots=off https://gwhaluan.github.io/tech-writing-with-mkdocs/

you get the following output on your console (or at least got, at the time of this writing):

``` shell
2018-12-04 13:19:24 URL:https://gwhaluan.github.io/tech-writing-with-mkdocs/ [9609/9609] -> "gwhaluan.github.io/tech-writing-with-mkdocs/index.html.tmp.tmp" [1]
2018-12-04 13:19:24 URL: https://gwhaluan.github.io/tech-writing-with-mkdocs/favicon.ico 200 OK
unlink: No such file or directory
2018-12-04 13:19:24 URL: https://gwhaluan.github.io/tech-writing-with-mkdocs/assets/stylesheets/application.451f80e5.css 200 OK
gwhaluan.github.io/tech-writing-with-mkdocs/assets/stylesheets/application.451f80e5.css.tmp: No such file or directory
unlink: No such file or directory
2018-12-04 13:19:25 URL: https://gwhaluan.github.io/tech-writing-with-mkdocs/assets/javascripts/modernizr.1aa3b519.js 200 OK
unlink: No such file or directory
2018-12-04 13:19:25 URL: https://gwhaluan.github.io/tech-writing-with-mkdocs/assets/fonts/material-icons.css 200 OK
gwhaluan.github.io/tech-writing-with-mkdocs/assets/fonts/material-icons.css.tmp: No such file or directory
unlink: No such file or directory
2018-12-04 13:19:25 URL: https://gwhaluan.github.io/tech-writing-with-mkdocs/stylesheets/hal.css 200 OK
gwhaluan.github.io/tech-writing-with-mkdocs/stylesheets/hal.css.tmp: No such file or directory
unlink: No such file or directory
2018-12-04 13:19:25 URL:https://gwhaluan.github.io/tech-writing-with-mkdocs/why/ [20913/20913] -> "gwhaluan.github.io/tech-writing-with-mkdocs/why/index.html.tmp.tmp" [1]
2018-12-04 13:19:25 URL:https://gwhaluan.github.io/tech-writing-with-mkdocs/config/ [27062/27062] -> "gwhaluan.github.io/tech-writing-with-mkdocs/config/index.html.tmp.tmp" [1]
2018-12-04 13:19:25 URL:https://gwhaluan.github.io/tech-writing-with-mkdocs/workflow/ [12263/12263] -> "gwhaluan.github.io/tech-writing-with-mkdocs/workflow/index.html.tmp.tmp" [1]
2018-12-04 13:19:25 URL:https://gwhaluan.github.io/tech-writing-with-mkdocs/nav/ [26665/26665] -> "gwhaluan.github.io/tech-writing-with-mkdocs/nav/index.html.tmp.tmp" [1]
2018-12-04 13:19:26 URL:https://gwhaluan.github.io/tech-writing-with-mkdocs/link/ [11841/11841] -> "gwhaluan.github.io/tech-writing-with-mkdocs/link/index.html.tmp.tmp" [1]
2018-12-04 13:19:26 URL:https://gwhaluan.github.io/tech-writing-with-mkdocs/tlpostproc/ [9361/9361] -> "gwhaluan.github.io/tech-writing-with-mkdocs/tlpostproc/index.html.tmp.tmp" [1]
2018-12-04 13:19:26 URL: https://gwhaluan.github.io/tech-writing-with-mkdocs/assets/javascripts/application.583bbe55.js 200 OK
unlink: No such file or directory
2018-12-04 13:19:26 URL: https://gwhaluan.github.io/tech-writing-with-mkdocs/scripts/mermaid.min.js 200 OK
unlink: No such file or directory
https://gwhaluan.github.io/tech-writing-with-mkdocs/link/non-existing.md:
Remote file does not exist -- broken link!!!
Found 1 broken link.

https://gwhaluan.github.io/tech-writing-with-mkdocs/link/non-existing.md

FINISHED --2018-12-04 13:19:26--
Total wall clock time: 2,5s
Downloaded: 7 files, 115K in 0,03s (3,62 MB/s)
```

wget needs to be invoked recursively to make it follow any links it discovers during parsing of page contents.
The `--spider` parameter prevents wget from downloading the pages and just behave like a webspider.
Note that this causes the log lines prefixed with `unlink:`, since wget still assumes that there are downloaded files.
If we do not exclude the lookup of a `robots.txt` file with `-e robots=off`, we get another error 404.

## Pipeline

You can always run this command locally, but then you would have to do it every time before or after you deploy your changes.
Ideally, it would be integrated into the deployment process or be part of your quality control.

For instance, wget can be run inside a docker task and even fail your build if there are broken links.

In order to not clutter your log, you can hack wget a little via:

    2>&1 | grep -B 1 'ERROR 404' && exit 1 || exit 0

This will redirect your stderr also to your stdout, pipe it into grep, looking through it for the string "ERROR 404" and then either exit in an error state or not.