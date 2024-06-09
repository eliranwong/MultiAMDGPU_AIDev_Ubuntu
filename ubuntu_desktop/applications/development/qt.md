# Install Qt

# Online Installer

1. Download online installer at: https://download.qt.io/official_releases/online_installers/qt-unified-linux-x64-online.run
2. Add permission:
> chmod +x qt-unified-linux-*.run
3. Run installer:
> ./qt-unified-linux-*.run

# Debian Packages

> sudo apt-get --no-install-recommends install libqt\*5-dev qt\*5-dev qml-module-qtquick-* qt*5-doc-html libqt5xml5 libqt5x11extras5 libqt5x11extras5-dev

# Qt6 for Python

> cd $HOME

> mkdir playqt6

> cd playqt6

> python3 -m venv venv

> source venv/bin/activate

> pip3 install PySide6

> echo 'alias qt6examples="source $HOME/playqt6/venv/bin/activate && cd $HOME/playqt6/venv/lib/python3.10/site-packages/PySide6/examples"' >> $HOME/.bashrc

# Tutorials

https://doc.qt.io/qtforpython/

https://www.pythonguis.com/pyside6/

https://doc.qt.io/qtforpython/

# Package with PyInstaller

> pip3 install --upgrade PyInstaller pyinstaller-hooks-contrib

Read https://doc.qt.io/qtforpython/deployment-pyinstaller.html
