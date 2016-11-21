
Nikola Quick Reference
======================

Use an existing `virtualenvwrapper` development environment:

``` bash
cd ~/Code/nikola
workon nikola
```

Create a new dev environment:

``` bash
cd ~/Code/nikola
pyenv local 3.5.2
mkvirtualenv nikola
pip install "Nikola[extras]"
```

Create new blog post:

``` bash
nikola new_post -f markdown -t "<title>" -a "Todd Radel"
```

Run a local dev server with auto update:

``` bash
nikola auto
```

Commit all changes and publish to Github:

``` bash
nikola github_deploy -m "<commit_reason>"
```
