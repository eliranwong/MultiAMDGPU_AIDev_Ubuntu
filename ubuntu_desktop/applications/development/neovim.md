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

# Add Nvim-Tree Plugin

Read more at https://github.com/nvim-tree/nvim-tree.lua/wiki/Installation

> nano ~/.config/nvim/lua/plugins/nvim-tree.lua

Add the following content

```
return {
  "nvim-tree/nvim-tree.lua",
  version = "*",
  lazy = false,
  dependencies = {
    "nvim-tree/nvim-web-devicons",
  },
  config = function()
    require("nvim-tree").setup {}
  end,
}
```

Run command `:NvimTreeOpen`

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
return {
  'kiddos/gemini.nvim',
  opts = {}
}
```

Edit the default settings at [optional]:

> nano ~/.local/share/nvim/lazy/gemini.nvim/lua/gemini/config.lua

# Common Operations

## Visual Mode

y - copy
p - paste
u - undo
Ctrl+r redo

## Soft Wrap

> echo "vim.opt.wrap=true >> ~/.config/nvim/init.lua"

## Open Terminal Window

SPACE+fT

## Close Window

Ctrl+w followed by 'c'

## Search with grep

> sudo apt install ripgrep 

> echo "vim.opt.grepprg='grep -nE' >> ~/.config/nvim/init.lua"

Grep root directory 'SPACE+/' or 'SPACE+g'

Grep open buffers 'SPACE+B'

Grep current directory 'SPACE+G'

Search and replace 'SPACE+sr'

Read more at lazyvim.org/keymaps

