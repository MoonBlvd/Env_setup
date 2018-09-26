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
