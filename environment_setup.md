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

#### setup SSH key and automatic pull
go to the workiong directory that you want automatic pull happen:

    vim .git/config

change the bare to be true

go to .git/hooks/, create a "post-receive" file, and type in following lines:

	#!/bin/bash
	TARGET="/home/brianyao/your-working-dir"
	GIT_DIR="/home/brianyao/your-working-dir/.git"
	BRANCH="master"

	while read oldrev newrev ref
	do
		# only checking out the master (or whatever branch you would like to deploy)
		if [[ $ref = refs/heads/$BRANCH ]];
		then
			echo "Ref $ref received. Deploying ${BRANCH} branch to production..."
			git --work-tree=$TARGET --git-dir=$GIT_DIR checkout -f
		else
			echo "Ref $ref received. Doing nothing: only the ${BRANCH} branch may be deployed on this server."
		fi
	done

save and run ```chmod -x post-receive``` to make it executible.
The this repo becomes a listening repo.

go to the mochine you used for coding, e.g. your desktop,go to the working directory

type in:

    git remote set-url --add --push origin username@xxxxx:your-working-dir/.git

this add a new push target to the git repo on your local machine, which is your server.


To avoid typing in the password of your server every time do:

    ssh-copy-id -i username@xxxxxx

this add the confidential of your server to your local machine.

#### install pip:
	sudo apt-get install python-pip
*Note: pip would be broken if run:*

	pip install --upgrate pip
     
#### installl virtualenv:
	pip install virtualenv
	pip install virtualenvwrapper
	
need to set the virtualenv wrapper directory for environment storage as follows:

	export WORKON_HOME=~/Envs
	source $HOME/.local/bin/virtualenvwrapper.sh
	
Make a virtualenv using python3:

	mkvirtualenv --python=/usr/bin/python3 your-virtualenv-name
	
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
	
#### install ipython and jupyter-notebook
Install ipython in an virtualenv:	

	mkvirtualenv py3 --python=python3
	pip install ipython
	pip install jupyter
#### install PyQt5
install the GPL version from Wheels

	pip install pyqt5
	
#### install Docker / run Jeremy's POSTGIS Docker 
To install Docker, refer to: sudo apt-get remove packagename
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
	
	
#### install QGIS 2.8 in ubuntu 16.04 xenial 
In ```/etc/apt/sources.list``` add the following:

	deb     http://qgis.org/debian xenial main
	deb-src http://qgis.org/debian xenial main
	
Run 

	sudo apt-get update
	sudo apt-get install qgis python-qgis qgis-plugin-grass
	
If there is ```NO_PUBKEY``` error,  add the missed key by:

	sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys *key value*
	
If there is dependency issues (might happen if the old version QGIS was not romoved correctly or clearly), try ```aptitude```:

	sudo aptitude install qgis python-qgis qgis-plugin-grass
		
#### install CUDA and Tensorflow
First, start a virtualenv for tensorflow

	mkvirtualenv --python=python3 tf
Then follow the instruction from: https://developer.nvidia.com/cuda-80-ga2-download-archive
**Please remember to check the nvidia driver and graphics drive version by:**
	nvidia-smi
~~**Do not install the latest CUDA-9.0 since its not supported by tensorflow yet.**
~~
	sudo dpkg -i cuda-repo-ubuntu1604-8-0-local-ga2_8.0.61-1_amd64.deb
	sudo apt-get update
	sudo apt-get install cuda
	
~~If you installed CUDA-9.0 or other CUDA by mistake, remove them by:~~
	
	sudo apt-get --purge remove <package_name>
	sudo apt autoremove
	
Tensorflow now supports CUDA-9.0 with cudnn-7.0!!!
	
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

Then to install tensorflow, go to the virtualenv, then run one of the following:

	pip install tensorflow      # Python 2.7; CPU support (no GPU support)
	pip3 install tensorflow     # Python 3.n; CPU support (no GPU support)
	pip install tensorflow-gpu  # Python 2.7;  GPU support
	pip3 install tensorflow-gpu # Python 3.n; GPU support 

Run simple code to validate the installation:

	import tensorflow as tf
	hello = tf.constant('Hello, TensorFlow!')
	sess = tf.Session()
	print(sess.run(hello))

#### install keras

With tensorflow installed, to install keras is very easy. In the tensorflow virtualenv, run:

	pip install keras
	
This will automatically install keras with tensorflow backend. More instructions can be seen here: https://keras.io/#installation

*Note: if you have to use CPU instead of cpu, add ```CUDA_VISIBLE_DEVICE=""``` to the beginning of the python 
cmd*

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
	
#### Other requirements for keras

	pip install h5py
	
ERROR:
	
	E tensorflow/stream_executor/cuda/cuda_blas.cc:366] failed to create cublas handle: CUBLAS_STATUS_NOT_INITIALIZED
	
	InternalError (see above for traceback): Blas SGEMM launch failed : m=361, n=1024, k=1024
	
To fix it, add the following lines in the code where keras/tensorflow is called:

	config = tf.ConfigProto()
	config.gpu_options.allow_growth=True
	session = tf.Session(config=config)
	
### Unreal Engin and CARLA

run CARLA as server

	./CarlaUE4.sh /Game/Maps/Town01 -carla-server -benchmark -fps=15
	
Run a client to communicate with the server, it's gonna start a pygame window so that the user can control the car, ```-i``` and ```-s``` indicate saving images and data:
	
	./manual_control.py -i -s 
### Install PCL 1.8.0
Install oracle -java8-jdk
	
	sudo add-apt-repository -y ppa:webupd8team/java && sudo apt update && sudo apt -y install oracle-java8-installer
	
Install dependencies

	sudo apt -y install g++ cmake cmake-gui doxygen mpi-default-dev openmpi-bin openmpi-common libusb-1.0-0-dev libqhull* libusb-dev libgtest-dev
	sudo apt -y install git-core freeglut3-dev pkg-config build-essential libxmu-dev libxi-dev libphonon-dev libphonon-dev phonon-backend-gstreamer
	sudo apt -y install phonon-backend-vlc graphviz mono-complete qt-sdk libflann-dev
	sudo apt -y install libflann1.8 libboost1.58-all-dev

Install Eigen package

	cd ~/Downloads
	wget http://launchpadlibrarian.net/209530212/libeigen3-dev_3.2.5-4_all.deb
	sudo dpkg -i libeigen3-dev_3.2.5-4_all.deb
	sudo apt-mark hold libeigen3-dev
 
Build Visualization ToolKit (VTK)
	
	wget http://www.vtk.org/files/release/7.1/VTK-7.1.0.tar.gz
	tar -xf VTK-7.1.0.tar.gz
	cd VTK-7.1.0 && mkdir build && cd build
	cmake ..
	make                                                                   
	sudo make install
	
Build PCL 

	cd ~/Downloads
	wget https://github.com/PointCloudLibrary/pcl/archive/pcl-1.8.0.tar.gz
	tar -xf pcl-1.8.0.tar.gz
	cd pcl-pcl-1.8.0 && mkdir build && cd build
	cmake ..
	make
	sudo make install
	
Remove unuseful packages

	cd ~/Downloads
	rm libeigen3-dev_3.2.5-4_all.deb VTK-7.1.0.tar.gz pcl-1.8.0.tar.gz
	sudo rm -r VTK-7.1.0 pcl-pcl-1.8.0
	

### Convert .pcap file to .pcd file 
open terminal, run: 
	
	roslaunch velodyne_pointcloud 32e_points.launch pcap:=(paht of your file) 

This will produce ensor_msgs/PointCloud2 

Then open new terminal, run:
	
	rosrun pcl_ros pointcloud_to_pcd input:=/velodyne_points _prefix:=(path to save it) 

This will output .pcd file

### Install ROS Lunar

Configure your Ubuntu repositories to allow "restricted," "universe," and "multiverse."

Setup your computer to accept software from packages.ros.org.

	sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'

Setup the key

	sudo apt-key adv --keyserver hkp://ha.pool.sks-keyservers.net:80 --recv-key 421C365BD9FF1F717815A3895523BAEEB01FA116

Install:

	sudo apt-get update
	sudo apt-get install ros-lunar-desktop-full

Initialize

	sudo rosdep init
	rosdep update 

Environment Setup

	echo "source /opt/ros/lunar/setup.zsh" >> ~/.zshrc
	source ~/.zshrc
	
Dependencies for buding packages

	sudo apt-get install python-rosinstall python-rosinstall-generator python-wstool build-essential
	
Above commands install ROS for default python, to run ROS in a virtualenv we need to install ```rospkg``` and ```catkin_pkg``` in that virtualenv:

	pip install rospkg
	pip install catkin_pkg	


###Install Caffe

Caffe is a popular CNN framework. The original repo is ```https://github.com/BVLC/caffe```. Some Caffe developers fork the original repo and change it per their own requirement, thus sometimes one need to build other forks of Caffe instead of the BVLC version. For example, for SSD there is ```https://github.com/manutdzou/KITTI_SSD```  and for RRC there is ```https://github.com/xiaohaoChen/rrc_detection```. For this example, we use the RRC repo.

First, make sure CUDA-8.0 (include cudnn) and opencv 3.3 (opencv 2.4 might be fine, but recomment 3.3) are insatlled. To properly install these two, please follow the steps in above sections. Then, clone the rrc repo to a none root directory (the make might fail if it's in a root directory):

	git clone https://github.com/xiaohaoChen/rrc_detection
	

	
Then modify the ```Makefile.config``` by 

	cd rrc_detection
	mv Makefile.config.example Makefile.config
	vim Makefile.config
	
Uncomment ```OPENCV_VERSION==3``` to declare that opencv 3.3 is used. Change ```CUDA_DIR``` to the CUDA root directory. To use python3, comment the two lines for python2 and uncomment python3 lines. Change the ```PYTHON_INCLUDE``` to the python you are using, e.g, the python for the virtualenv that you are using. Uncomment ```WITH_PYTHON_LAYER := 1``` to support layers written in python. Add ```/usr/include/hdf5/serial``` to ```INCLUDE_DIRS```, add ```/usr/lib/x86_64-linux-gnu/hdf5/serial``` to ```LIBRARY_DIRS```.

Then make and test Caffe:

	make all -j8
	make pycaffe
	make test
	make runtest
	make distribute
	
If  there is the error ```cannot find -lboost_python3``` when make, then link the existed file to the proper name by do the following:

	cd /usr/lib/x86_64-linux-gnu
	sudo ln -s libboost_python-py35.so libboost_python3.so 

If there are other errors, please contact.
If no errors, then change the folder name from ```rrc_detection``` to ```caffe-rrc``` and move it a directory that you would to perminantly keep it. Here we suggest ```/usr/lib/caffe-rrc```

Then add two lines to ```~/.zshrc``` or ```~/.bashrc```

	export CAFFE_ROOT="/usr/lib/caffe-rrc"
	export PYTHONPATH=$CAFFE_ROOT/python:$PYTHONPATH

Then ```source ~/.zshrc``` or ```source ~/.bashrc```. 
sudo apt-get remove packagename
By building Caffe in this way, one can use caffe and all its python api using the python that added to the ```Makefile.config```.

###Install Optimization Tools
The optimization tool used here is PICOS

To install PICOS, download the source from http://picos.zib.de/download.html#download, then go to the virtualenv and run ```python setup.py install```. 

PICOS can use different solvers, the default one is ```cvxopt```, to run quadratic programming (QP), we need ```gurobi```.

To install ```gurobi```, download from the website https://www.gurobi.com/downloads/gurobi-optimizer and run the following

	python setup.py install

Then add the path to the ```~/.zshrc``` or  ```~/.bashrc```

	export GUROBI_HOME="/home/brianyao/Documents/gurobi702/linux64"
	export PATH="${PATH}:${GUROBI_HOME}/bin"
	export LD_LIBRARY_PATH="${LD_LIBRARY_PATH}:${GUROBI_HOME}/lib"
	
Change the ```GUROBI_HOME``` to your own gurobi directory.
