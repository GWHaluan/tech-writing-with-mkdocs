# Link Checking

mkdocs does not come with integrated link checking.
In a living documentation, file names as well as section titles are going to change on a regular basis.
Every time that happens, any links to those files or section titles inevitably break and need to be fixed.
Thus it would be nice to have an automated process that detects these broken links so you do not have to search for them yourself.

The solution sketched below uses a recursive wget command during a build process.

## wget

GNU Wget is a program that retrieves web content.
It can be utilized as a link checker because it will return a 404 if it cannot follow links when invoked recursively.

Take for example this [broken link](non-existing.md#no-title) (relative Markdown link to `non-existing.md#no-title`).

Now, if you invoke wget like so:

    wget --recursive --no-verbose --reject-regex=/[0-9\.]+/ https://gwhaluan.github.io/tech-writing-with-mkdocs/

<!--- --domains=documentation.peakwork.lan -->
<!--- 2>&1 | grep -B 1 'ERROR 404' && exit 1 || exit 0 -->