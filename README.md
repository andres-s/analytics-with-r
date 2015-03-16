Analytics with R
----------------

#### Build ####

To build you will need to install pandoc, and then run from the root directory of the repo:

```bash
$ pandoc -V slidy-url=slidy --self-contained -i -s -t slidy -o slides.html r-for-data-integrity.md
```
