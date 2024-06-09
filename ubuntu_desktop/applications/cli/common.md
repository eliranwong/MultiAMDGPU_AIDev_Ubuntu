# Examples of common commands

# wget

Use wget to download a repository in zip format:

> wget https://github.com/eliranwong/MultiAMDGPU_AIDev_Ubuntu/archive/main.zip

# Setup mlocate

To install:<br>
> sudo apt install mlocate

To create database:<br>
> sudo /etc/cron.daily/mlocate

To manually update the database:<br>
> sudo updatedb

To run:

> locate [search_item]

or

> mlocate [search_item]

e.g. find an item in current directory

> mlocate [search_item] | grep $(pwd)

# Common tasks of text processing

To split a single text file into multiple ones, with maximum of 500 lines each, e.g.:<br>
> split -d -l 500 [filename] [optional_prefix_name]

To sort TAB-delimited text file by first 3 columns:<br>
> sort -b -n -t "[TAB HERE]" -k 1,3 test.txt > test2.txt

To add line numbers to text file, starting from number 1:<br>
> nl -nln -v 1 -s "[TAB HERE]" test.txt > test2.txt<br>
[Remarks: -v 1 -s "[TAB HERE]" is implemented by default, could be omitted.]

To change file extensions of multiple files:<br>
> for file in *.html; do mv "$file" "${file%.html}.txt"; done

To add an empty line at the end of a file:<br>
> echo "" >> test.txt

To add an empty line in multiple files:<br>
> for file in *.txt; do echo "" >> $file; done

To combine multiple files in a single file:<br>
> cat *.txt > combined.txt

Tutorial on string manipulation:<br>
https://www.tldp.org/LDP/abs/html/string-manipulation.html

More at: http://www.tldp.org/LDP/abs/html/

# Search & Replace

* Simple tasks with sed:

> sed -E -i.bak 's/[search]/[replace]/g' [file]

e.g. an example of back references:

> echo "test testing" | sed -E 's/(t)(e)s\1/\2/g'

e.g. executing multiple commands

> echo "test testing" | sed -E -e 's/(t)(e)s\1/\2/g;s/i/I/g'

> sed -E -i.bak -f myScript.sed inputFile.txt

e.g. remove file extension:

> echo "test.txt.html" | sed s/\\.[^\\.]*$//

e.g. open Thunar from Windows Explorer:

https://github.com/eliranwong/wsl2/blob/master/file_manager/thunar.md#launch-thunar-from-windows-context-menu

* Simple tasks with awk / gawk:

> echo "My_name_is_Wong" | awk -F '_' '{surname=$4; $4="Eliran"; print $4 " " surname; print $0 " " surname}'

> echo "My_name_is_Wong" | awk -F '_' '{gsub($4,"Eliran_Wong",$0); print$0}'

e.g. append text before file extension

> echo "test.text.txt.html" | awk -F '.' '{gsub($(NF-1), $(NF-1)"_new", $0); print$0}'

e.g. open Windows Explorer from Thunar:

https://github.com/eliranwong/wsl2/blob/master/file_manager/thunar.md#by-right-clicking-a-folder

# Convert an image file to html

> echo '\<img src="data:image/png;base64,'$(base64 test.png)'"\>' > test.html

# A Search & Replace Utility, written with python:

https://github.com/eliranwong/bible-verse-parser/blob/master/RegexSearch.py

Read usage & examples at:

https://github.com/eliranwong/wsl2/blob/master/cli_tools/resr.md

# rar & unrar

To list files in a rar file, e.g. file.rar:

> unrar l file.rar

To extract a rar file, e.g. file.rar:

> unrar e file.rar

To pack a folder, e.g. myFolder, into a rar file, e.g. file.rar:

> rar a file.rar myFolder

# sqlite3 Import / Export

Import a TAB-delimited text file into a table:<br>
sqlite3 FILE.sqlite<br>
> .separator "\t"<br>
> .import DATA.csv TABLENAME<br>
> VACUUM;<br>
> .quit<br>

To export without header:<br>
> sqlite3 -separator "[TAB HERE]" FILE.sqlite "SELECT * FROM TABLENAME;" > output_filename.csv

To export with header:<br>
> sqlite3 -header -separator "[TAB HERE]" FILE.sqlite "SELECT * FROM TABLENAME;" > output_filename.csv

To delete all records in a table:<br>
> sqlite3 FILE.sqlite "DELETE FROM TABLENAME; VACUUM;"

To delete a table:<br>
> sqlite3 Bible_Promises.book "DROP TABLE TABLENAME;"

Remarks: To enter a TAB character on terminal:<br>
Ctrl + v + TAB

# Conversion between Traditional and Simplified Chinese

Convert traditional Chinese to simplified Chinese:<br>
> opencc -i [inputFile] -o [outputFile] -c t2s.json

Convert simplified Chinese to traditional Chinese:<br>
> opencc -i [inputFile] -o [outputFile] -c s2t.json

# Working on Xresources

To add / update configurations:<br>
nano ~/.Xresources<br>
[Edit ~/.Xresources]<br>
xrdb -merge ~/.Xresources

To replace / remove configurations:<br>
xrdb -query -all > ~/.Xresources<br>
[Edit ~/.Xresources]<br>
xrdb ~/.Xresources

# Information
* check cpu

lscpu

* check storage

df -h

* check size of current directory

du -sh

* check memory

free -h

* Display File(s) in Tree

For example:<br>
tree -L 1 /

# Force quit an app

Make use of "aux ps" to locate a process and "kill" to quit.

Alternatively, use "pkill"

# Install & Uninstall .deb package

To Install a downloaded .deb file:<br>
sudo dpkg -i packagename.deb<br>
OR<br>
sudo apt install ./packagename.deb<br>
OR<br>
Use chrome os "Files" app, right-click a .deb file and select "Install with Linux"

To Remove an installed package:<br>
sudo dpkg -r packagename<br>

Alternatively, open chrome os "Files" app (Alt+Shift+m), right click a *.deb file and select "Install with Linux"

# Compile from source, an example:

tar xvfz [package].tar.gz<br>
cd [unpacked folder]<br>
./configure<br>
make<br>
make install<br>

# Trouble-shoot error on loading file(s):

1. Use "<a href="https://github.com/eliranwong/Chrome-OS-Linux/blob/master/README.md#setup-mlocate">mlocate</a>" to find full path of the missing file:<br>
mlocate [missing file]<br>

2. Use "dpkg -S" to find the package(s) owning file(s):<br>
dpkg -S [full file path]<br>

3. re-install the package, indicated in step in the result of step (2)<br>

For example, https://community.rstudio.com/t/installation-error-cannot-find-libsmime3-so/30646

# Upgrade

Use "sudo apt dist-upgrade" instead of "sudo apt upgrade"

# Clean up storage

sudo apt clean<br>
sudo apt autoremove --purge<br>

# Launch a GUI app through Terminal WITHOUT Blocking the Terminal

e.g. To launch leafpad through terminal:

geany &<br>
[Closing the terminal closes leafpad too.]

OR 

geany & disown<br>
[Leafpad keeps running even the terminal is closed.]

# vi editor

ZZ - Save and exit<br>
:q! - discard all changes, since the last save, and exit<br>
:w - save file but don't exit<br>
:wq - again, save and exit<br>

[<a href='https://github.com/eliranwong/Chrome-OS-Linux/blob/master/temp/ViCheatSheet.pdf'>ryanstutorials cheat sheet</a>]

# Navigation with "man" or "less"

```
SPACE = next page
b = previous page
q = quit
-i = swap case-sensitive searches
/ = search for plain text
? = search for a pattern
```

# Restart Network

> sudo /etc/init.d/networking restart

or

> sudo /etc/init.d/networking stop

> sudo /etc/init.d/networking start

or

> sudo systemctl restart networking
