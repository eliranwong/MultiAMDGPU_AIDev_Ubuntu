# Install Go

https://go.dev/doc/install

For example, install go version go1.19.4 linux/amd64

> wget https://go.dev/dl/go1.19.4.linux-amd64.tar.gz

> sudo rm -rf /usr/local/go

> sudo tar -C /usr/local -xzf go1.19.4.linux-amd64.tar.gz

> echo "PATH=$HOME/go/bin:/usr/local/go/bin:$PATH" >> ~/.profile

# Install Fyne

> sudo apt install gcc libgl1-mesa-dev xorg-dev

> go install fyne.io/fyne/v2/cmd/fyne@latest

> go install fyne.io/fyne/v2/cmd/fyne_demo@latest

# Install Fyne Module in a Project

> go get fyne.io/fyne/v2

> go mod tidy

# Set GOPATH

https://github.com/golang/go/wiki/SettingGOPATH#zsh
