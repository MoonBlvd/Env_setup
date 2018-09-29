# Fully setup Jetson TegraX2

## Flashing the OS (R28.2.1) and installing Jetpack3.3

### Setup ethernet connect and wifi connection

**(Skip this section if the ethernet for TX2 and host PC work well!!)**

If the Jetson TX2 cannot connect to internet with ethernet port, we must setup static IP and let it work along with wifi so that it can connect to the host PC and internect simultaneously (!!) **Note that by doing this, the host PC won't have internect connect unless it has wifi. This problem is not solved yet!**

To setup the static IP address for TX2, add the follwoing lines to ```/etc/network/interfaces```:

	auto lo
	iface lo inet loopback
	
	auto eth0
	iface eth0 inet static
	address 192.168.0.2
	netmask 255.255.255.0
	network 192.168.0.0

To setup the static IP address for host PC, add the same lines to the same file except that the address should be different, e.g. ```address 192.168.0.4``` .

**Note that Jetpack builds opencv only for python2 by default. We will show how to build opencv for python3 later.**

**Note that tensorrt python API doesn't support Jetson yet.**

## Install basic dependencies over the whole system
### install opencv 3.4.0

Follow this link <https://jkjung-avt.github.io/opencv3-on-tx2/>. 
Change from 3.4.0 to 3.4.2 which is the newer version.
Change the cmake command to the following to indicate which python to build for.

	cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local \
	      -D WITH_CUDA=ON -D CUDA_ARCH_BIN="6.2" -D CUDA_ARCH_PTX="" \
	      -D WITH_CUBLAS=ON -D ENABLE_FAST_MATH=ON -D CUDA_FAST_MATH=ON \
	      -D ENABLE_NEON=ON -D WITH_LIBV4L=ON -D BUILD_TESTS=OFF \
	      -D BUILD_PERF_TESTS=OFF -D BUILD_EXAMPLES=OFF \
	      -D WITH_QT=ON -D WITH_OPENGL=ON \
	      -D PYTHON=$/usr/lib/python3.5 \
	      -D PYTHON_DEFAULT_EXECUTABLE=/usr/bin/python3 \
	      -D PYTHON_LIBRARY=/usr/lib/aarch64-linux-gnu/libpython3.5m.so ..

If use virtualenv, **don't** select the python in virtualenv. Instead, mv the ```cv2*.so``` file to the ```site-packages``` directory under the desired virtualenv after build. 

### Install Tensorflow and Keras
There are some tricks to get keras with tensorflow backend work on TX2

* We installed the keras 2.2.0 on TX2, and the Mask-RCNN repo we are using shots error. Do the following to fix it:

    in mrcnn/models.py change all ```topology``` to ```saving```
* We may meet OOM issue. Firstly check the free memory by ```free -h```, if free memory is small, reboot or clear the cache/buffer.

* We may see "gpu sync failed" error, this is tensorflow error. Add following lines to set gpu usage for tensorflow/keras:

		import tensorflow as tf
		from keras.backend.tensorflow_backend import set\_session
		config = tf.ConfigProto()
		#config.gpu_options.per_process_gpu_memory_fraction = 0.3
		config.gpu_options.allow_growth = True
		set_session(tf.Session(config=config))
    
### Install PyTorch

To build pytorch, follow this .sh file:

	# pyTorch install script for NVIDIA Jetson TX1/TX2,
	# from a fresh flashing of JetPack 2.3.1 / JetPack 3.0 / JetPack 3.1

	# for the full source, see jetson-reinforcement repo:
	#   https://github.com/dusty-nv/jetson-reinforcement/blob/master/CMakePreBuild.sh
	#
	# note:  pyTorch documentation calls for use of Anaconda,
	#        however Anaconda isn't available for aarch64.
	#        Instead, we install directly from source using setup.py
	sudo apt-get install python-pip
	
	# upgrade pip
	pip install -U pip
	pip --version
	# pip 9.0.1 from /home/ubuntu/.local/lib/python2.7/site-packages (python 2.7)
	
	# clone pyTorch repo
	git clone http://github.com/pytorch/pytorch
	cd pytorch
	git submodule update --init
	
	# install prereqs
	sudo pip install -U setuptools
	sudo pip install -r requirements.txt
	
	# Develop Mode:
	python setup.py build_deps
	sudo python setup.py develop
	
	# Install Mode:  (substitute for Develop Mode commands)
	#sudo python setup.py install

### ADD SWAP SPACE
    
    # Create a swapfile for Ubuntu at the current directory location
    fallocate -l 8G swapfile
    # List out the file
    ls -lh swapfile
    # Change permissions so that only root can use it
    chmod 600 swapfile
    # List out the file
    ls -lh swapfile
    # Set up the Linux swap area
    mkswap swapfile
    # Now start using the swapfile
    sudo swapon swapfile
    # Show that it's now being used
    swapon -s
 
