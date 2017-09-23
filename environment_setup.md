## Set up a ubuntu 16.04 working environment.

#### update apt-get
	sudo apt-get update
#### install zsh:
	sudo apt-get install zsh
	sudo apt-get install git-core
	wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | zsh
	"Password: chsh: PAM: Authentication failure"
	chsh -s $(which zsh)
	"If it doesn't work, check which zsh, then type in chsh and change the directory of the default shell to the output of which zsh"	
	sudo shutdown -r 0
	

#### install pip:
	sudo apt-get install python-pip
	_Note: pip would be broken if run:_ 
		pip install --upgrate pip 
     
#### installl virtualenv:
	pip install virtualenv
	pip install virtualenvwrapper
	-------------------
	need to set the virtualenv wrapper directory for environment storage as follows:
	"export WORKON_HOME=~/Envs"
	"source $HOME/.local/bin/virtualienvwrapper.sh"
	
#### install Markdown editor Remarkable:
	_Download the newest .deb file from website_
	sudo apt-get install gdebi-core
	sudo gdebi XXX.deb

####  install spatialite
	"Refer to : https://timogrossenbacher.ch/2013/02/how-to-install-spatialite-and-spatialite-gui-on-ubuntu-12-04/"
	---------------------------------------------
	sudo apt-get update 
	sudo apt-get install libsqlite3-dev 
	sudo apt-get install libproj-dev 
	sudo apt-get install libgeos-dev
	sudo apt-get install zlib1g-dev
	sudo apt-get install libxml2-dev
	
	./configure --enable-freexl=no
	make -j4 // use 4 cores
	sudo make install-strip
	
	find out the directory of mod_spatialite.so and set the LD_LIBRARY_PATH to it, add following line to ~/.zshrc:
	
	export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH
	
	note: if the installation doesn't work, we can manualy copy the .so files to target directory.
	
	install gui:
	sudo apt-get install spatialite-gui
	
	
#### install ipython
	start a virtualenv by:
	
	mkvirtualenv py3 --python=python3
	
	then install ipython:
	
	pip install ipython
#### install PyQt5
	*Install the GPL version from Wheels*
	pip install pyqt5
