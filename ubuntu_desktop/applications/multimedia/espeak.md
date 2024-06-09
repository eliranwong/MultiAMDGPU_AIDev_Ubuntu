# espeak

espeak is an offline text-to-speech tool.

# Install espeak

> sudo apt install espeak -y

# Check for available language

> espeak --voices

# Install Additional Data for eSpeak

You may get error messages by running the following examples, because some data are not installed:

> espeak -v zh 你好

> espeak -v zhy 你好

Keep reading if you need to install Chinese and Russian data.

Source of information at: http://espeak.sourceforge.net/data/

We found that it is not clear from the link above on how to install additional data, we therefore share some notes.

Run the following steps to install additional data

First, download, unzip the source code, and go to directory "dictsource":

> cd ~/Downloads

> wget http://sourceforge.net/projects/espeak/files/espeak/espeak-1.48/espeak-1.48.04-source.zip

> unzip espeak-1.48.04-source.zip

> cd espeak-1.48.04-source/dictsource/

Install additional data on Chinese - Mandarin:

> wget http://espeak.sourceforge.net/data/zh_listx.zip

> unzip zh_listx.zip

> sudo espeak --compile=zh

Install additional data on Chinese - Cantonese:

> wget http://espeak.sourceforge.net/data/zhy_list.zip

> unzip zhy_list.zip

> sudo espeak --compile=zhy

Install additional data on Chinese - Russian:

There are several versions of Russian data.  We checked our installed version of espeak is 1.48 by running:

> espeak --version

Therefore, we download the 1.48 version of Russian data and install it:

> wget http://espeak.sourceforge.net/data/ru_dict-48.zip

> unzip ru_dict-48.zip

> sudo espeak --compile=ru

# Install & Setup espeakedit [optional]

> sudo apt install espeakedit -y

> mkdir -p ~/espeak-data

> cp -r /usr/lib/x86_64-linux-gnu/espeak-data/* ~/espeak-data/

To run:

> espeakedit
