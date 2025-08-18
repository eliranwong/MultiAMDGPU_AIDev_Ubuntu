# Install Neovim

https://neovim.io/

To check architecture:

> uname -m

To install, e.g. on a x86_64 device:

```
curl -LO https://github.com/neovim/neovim/releases/latest/download/nvim-linux-x86_64.tar.gz
sudo rm -rf /opt/nvim
sudo tar -C /opt -xzf nvim-linux-x86_64.tar.gz
export PATH="$PATH:/opt/nvim-linux-x86_64/bin"
echo 'export PATH="$PATH:/opt/nvim-linux-x86_64/bin"' >> ~/.bashrc 
```

# Install LazyVim

https://www.lazyvim.org/installation

```
git clone https://github.com/LazyVim/starter ~/.config/nvim
rm -rf ~/.config/nvim/.git
nvim
```

# Add Plugin - gemini.nvim

Official Documentation

* https://github.com/kiddos/gemini.nvim

* https://github.com/kiddos/gemini.nvim/blob/master/doc/gemini.nvim.txt

Basic:

Export Gemini API Key:

> echo 'export GEMINI_API_KEY="xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"' >> ~/.bashrc

Edit the file:

> nano ~/.config/nvim/lua/plugins/gemini.lua

Add the following content:

```
{
  'kiddos/gemini.nvim',
  opts = {}
}
```

Edit the default settings at [optional]:

> nano ~/.local/share/nvim/lazy/gemini.nvim/lua/gemini/config.lua