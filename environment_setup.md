## Set up a ubuntu 16.04 working environment.

#### update apt-get
	sudo apt-get update
#### install zsh:
	sudo apt-get install zsh
	sudo apt-get install git-core
	wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | zsh
	chsh -s $(which zsh)
	sudo shutdown -r 0
* Password: chsh: PAM: Authentication failure"
* If it doesn't work, check which zsh, then type in chsh and change the directory of the default shell to the output of which zsh"	

#### install pip:
	sudo apt-get install python-pip
*Note: pip would be broken if run:*

	pip install --upgrate pip
     
#### installl virtualenv:
	pip install virtualenv
	pip install virtualenvwrapper
	
need to set the virtualenv wrapper directory for environment storage as follows:

	export WORKON_HOME=~/Envs
	source $HOME/.local/bin/virtualienvwrapper.sh
	
#### install Markdown editor Remarkable:
Download the newest .deb file from website:

	sudo apt-get install gdebi-core
	sudo gdebi XXX.deb

####  install spatialite
Refer to : https://timogrossenbacher.ch/2013/02/how-to-install-spatialite-and-spatialite-gui-on-ubuntu-12-04/"

	sudo apt-get update 
	sudo apt-get install libsqlite3-dev 
	sudo apt-get install libproj-dev 
	sudo apt-get install libgeos-dev
	sudo apt-get install zlib1g-dev
	sudo apt-get install libxml2-dev
	./configure --enable-freexl=no
	make -j4 // use 4 cores
	sudo make install-strip
	
find out the directory of ```mod_spatialite.so```and set the ```LD_LIBRARY_PATH``` to it, add following line to ```~/.zshrc```:

	export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH
*note: if the installation doesn't work, we can manualy copy the .so files to target directory.*

Install gui:

	sudo apt-get install spatialite-gui
	
#### install ipython
Install ipython in an virtualenv:	

	mkvirtualenv py3 --python=python3
	pip install ipython

#### install PyQt5
install the GPL version from Wheels

	pip install pyqt5
	
#### install Docker / run Jeremy's POSTGIS Docker 
To install Docker, refer to: 
https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/#install-using-the-repository

To gIve authority to the account, first check the group:

	groups
Add group "docker" and add the user account to docker

	sudo groupadd docker
	sudo gpasswd -a $USER docker
Login/ou then check:

	docker run hello-world
To  access and explore a container in bash run:

	docker exec -t -i containername /bin/bash
To download and run Jeremy's POSTGIS Docker, refer to:

	https://bitbucket.org/umich_a2sys/postgis-osm/overview
Or directly do this:

	git clone https://BrianYao@bitbucket.org/umich_a2sys/postgis-osm.git
	cd postgis-osm
	docker pull jeremybyu/postgis-osm
	docker run --name osm -d -p 5050:5050 -p 5432:5432 -v ${PWD}:/home/postgis-osm jeremybyu/postgis-osm
	docker exec -i -t osm bash -c 'cd ${IMPOSM_HOST} && ${IMPOSM_BIN} import -config config.json -read DATAFILENAME.osm.pbf -overwritecache'
	docker exec -i -t osm bash -c 'cd ${IMPOSM_HOST} && ${IMPOSM_BIN} import -config config.json -write'

To view the database using PgAdmin:

	docker exec -i -t osm bash -c 'python $PGADMIN/pgAdmin4.py'
To convert PostGIS to Spatialite:

	sudo add-apt-repository ppa:ubuntugis/ppa && sudo apt-get update 
	sudo apt-get install gdal-bin
	ogr2ogr -f SQLite -dsco SPATIALITE=yes DB_FILE_NAME.db PG:"dbname=osm user=osm password=osm host='localhost' port='5432'" import.osm_buildings
	
**One could run imposm in local instead of in the Docker container.** To do this, we should install imposm3:

 Download the latest binary file (unofficial) from: https://imposm.org/static/rel/
 
Extract and copy the ```lib``` folder and the ```imposm3``` file to the working directory.

Then one could run the following command from the terminal to import binary osm data and output spatialite db file:

	./DIR/imposm3 import -config imposm3/config.json -read FILENAME -appendcache
	./DIR/imposm3 import -config imposm3/config.json -write
	ogr2ogr -f SQLite -dsco SPATIALITE=yes ../OSM_database/data/USA_osm.db PG:"dbname=osm user=osm password=osm host='localhost' port='5432'" import.osm_roads

	

	
	
	
#### install sublime3
	wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | sudo apt-key add -
	echo "deb https://download.sublimetext.com/ apt/stable/" | sudo tee /etc/apt/sources.list.d/sublime-text.list
	sudo apt-get update
	sudo apt-get install sublime-text
	
#### install scipy, , matplotlib, pandas and seaborn

	sudo apt-get update
	sudo apt-get install python-scipy python-pandas python-matplotlib python3-tk
	pip install seaborn
However, apt-get only installs globally, to install for virtualenv, run: 

	pip install numpy
	pip install scipy
	pip install matplotlib
	pip install pandas
	pip install seaborn
	pip install scikit-learn
#### install Folium, geopandas, psycopg
Folium is a nice too for geometric plotting 

	pip install folium
	pip install geopandas
	
*Note*: dependencies will be installed when install geopandas, but there may be ```Python.h``` error. In that case, run:

	sudo apt-get update
	sudo apt-get install python3-dev

Psycopg is a PostgreSQL adapter for the Python programming language. It is a wrapper for the libpq, the official PostgreSQL client library.

	pip install psycopg2
	
#### Branca colormap tips
Add more colormaps to the source code of branca:

	python2.7(or3.5)/site-package/branca/colormap.py
	
The default logrithm color map in ```branca``` is natural logrithm, we could change it to 10-based logrithm by changeing the following line:

	index = [math.exp(math.log(min_) + i * (math.log(max_) -       math.log(min_)) * 1./n ) for i in range(1+n)]
	
	
#### install CUDA and Tensorflow
First, start a virtualenv for tensorflow

	mkvirtualenv --python=python3 tf
Then follow the instruction from: https://developer.nvidia.com/cuda-80-ga2-download-archive
**Please remember to check the nvidia driver and graphics drive version by:**
	nvidia-smi
**Do not install the latest CUDA-9.0 since its not supported by tensorflow yet.**

	sudo dpkg -i cuda-repo-ubuntu1604-8-0-local-ga2_8.0.61-1_amd64.deb
	sudo apt-get update
	sudo apt-get install cuda
	
If you installed CUDA-9.0 or other CUDA by mistake, remove them by:
	
	sudo apt-get --purge remove <package_name>
	sudo apt autoremove
	
Then add the path of cuda to ```~/.zshrc```

	$ export PATH=/usr/local/cuda-8.0/bin${PATH:+:${PATH}}
	
Then install cudnn6.0, first download the library from https://developer.nvidia.com/rdp/cudnn-download, the follow the install Guide in that website. 

	sudo cp cuda/include/cudnn.h /usr/local/cuda/include
	sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
	sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*

Add the following lines to ```~/.zshrc```

	export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:	$LD_LIBRARY_PATH
	export CPPFLAGS='-I/usr/local/cuda-8.0/include'
	export LDFLAGS='-L/usr/local/cuda-8.0/lib64'
	export LIBS='-lcudnn'
	
#### install keras

With tensorflow installed, to install keras is very easy. In the tensorflow virtualenv, run:

	pip install keras
	
This will automatically install keras with tensorflow backend. More instructions can be seen here: https://keras.io/#installation

#### install opencv 3.3.0
	
The ```opencv 3.2.0``` had python3 unavailable issue when cmake, thus we install ```opencv 3.3.0```. Please follow the instruction in this link:	
https://github.com/BVLC/caffe/wiki/OpenCV-3.3-Installation-Guide-on-Ubuntu-16.04

First, create and enter a building directory

	mkdir build
	cd build/
	
When ```cmake```, turn on ffmpeg and declare the specific directory of python, also declare the camke install directory, see the following command:
	
	cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=$WORKON_HOME/tf/ -D PYTHON=$WORKON_HOME/tf/lib/python3.5 -D PYTHON_DEFAULT_EXECUTABLE=$WORKON_HOME/tf/bin/python -D PYTHON3_PACKAGES_PATH=$WORKON_HOME/tf/lib/python3.5/site-packages -D PYTHON_LIBRARY=/usr/lib/x86_64-linux-gnu/libpython3.5.so -D FORCE_VTK=ON -D WITH_TBB=ON -D WITH_V4L=ON -D WITH_QT=ON -D WITH_OPENGL=ON -D WITH_CUBLAS=ON -D WITY_FFMPEG=ON -D CUDA_NVCC_FLAGS="-D_FORCE_INLINES" -D WITH_GDAL=ON -D WITH_XINE=ON -D BUILD_EXAMPLES=ON ..

Please carefully check the output of cmake, expecially the installation path and the python it uses. Also check whether ffmpeg and other modules are on.

Then make:

	make -j8
	
There can be warnings about nvcc which are save to ignore.
	
Then make install:

	sudo make install
	sudo /bin/bash -c 'echo "/usr/local/lib" > /etc/ld.so.conf.d/opencv.conf'
	sudo ldconfig
	sudo apt-get update
	
Finally, reboot the system and add the path to ```PYTHONPATH```:

	export PYTHONPATH=$WORKON_HOME/tf/lib/python3.5/dist-packages:$PYTHONPATH
	
If opencv is not necessary,  a very simple alternative is imageio. To install, run this cmd in the virtualenv:

	pip install imageio 	