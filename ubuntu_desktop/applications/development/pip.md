Trouble-shouting - There was an error checking the latest version of pip

First, try:

> python3 -m pip install --upgrade pip

If upgrading pip makes no difference, try to clear the pip caches:

## Linux

Run this command in the terminal.

> rm -r ~/.cache/pip/selfcheck/

## OSX

Run this command in the terminal.

> rm -r ~/Library/Caches/pip/selfcheck/

## Windows

Run this using PowerShell.

> rm -r $env:LOCALAPPDATA\pip\cache\selfcheck\
