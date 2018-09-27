# Fully setup Jetson TegraX2

## Flashing the OS (R28.2.1) and installing Jetpack3.3

#### Setup ethernet connect and wifi connection
If the Jetson TX2 cannot connect to internet with ethernet port, we must setup static IP and let it work along with wifi so that it can connect to the host PC and internect simultaneously (!!) **Note that by doing this, the host PC won't have internect connect unless it has wifi. This problem is not solved yet!**

To setup the static IP address for TX2:

To setup the static IP address for host PC: 

**Note that Jetpack builds opencv only for python2 by default.**
**Note that tensorrt python API doesn't support Jetson yet.**

## Install basic dependencies over the whole system


## Install Tensorflow and Keras
There are some tricks to get keras with tensorflow backend work on TX2

* We installed the keras 2.2.0 on TX2, and the Mask-RCNN repo we are using shots error. Do the following to fix it:

    in mrcnn/models.py change all ```topology``` to ```saving```
* We may meet OOM issue. Firstly check the free memory by ```free -h```, if free memory is small, reboot or clear the cache/buffer.

* We may see "gpu sync failed" error, this is tensorflow error. Add following lines to set gpu usage for tensorflow/keras:

    import tensorflow as tf
    from keras.backend.tensorflow_backend import set_session
    config = tf.ConfigProto()
    #config.gpu_options.per_process_gpu_memory_fraction = 0.3
    config.gpu_options.allow_growth = True
    set_session(tf.Session(config=config))

 
 
