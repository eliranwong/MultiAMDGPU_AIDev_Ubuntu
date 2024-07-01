# Download

Download firefox package (*.tar.bz2) into `Downloads` folder:

https://www.mozilla.org/firefox/linux/

Run in terminal:

```
sudo apt install -y libstdc++5 libdbus-glib*
cd ~
tar xjf Downloads/firefox-*.tar.bz2
rm Downloads/firefox-*.tar.bz2
echo 'alias "firefox=/home/ubuntu/firefox/firefox &>/dev/null & disown"' >> ~/.bashrc
source ~/.bashrc
```

To run:

> firefox