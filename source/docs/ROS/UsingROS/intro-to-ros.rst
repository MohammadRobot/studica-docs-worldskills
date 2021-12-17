The ROS Package
===============

What is ROS?
------------

ROS (Robot Operating System) is an open-source collection of software frameworks for robot development, it allows for functionalities such as low-level device control, message-passing between processes, and package management. The tools and libraries available make it possible to build, write, and run code across multiple computers. The main advantage of ROS is its peer-to-peer network, this allows for communication across multiple nodes and devices without requiring an auxilliary server computer or server software. This means processes distributed across various machines can interact using the ROS communication framework.

Why Noetic?
^^^^^^^^^^^
    
Essentially, a distribution (distro) is a set of ROS packages rolled up into a release, there are various distributions of ROS each with different functionalities to suit the needs of different robots.

.. figure:: images/ros_noetic.JPG
    :align: center
    :width: 40%
    
ROS Noetic is currently the final and latest version of ROS 1 available with an EOL date set for May 2025, this means another distribution of the operating system will not be released for ROS 1. However, Noetic is an LTS release meaning it will have support throughout its lifetime though no major functionality will be added. Moreover, ROS 2 is only available on Ubuntu and not Raspbian, which is the official supported operating system for the Raspberry Pi. Raspbian is required as the `VMX-pi HAL Library <https://www.kauailabs.com/public_files/vmx-pi/apidocs/hal_cpp/html/index.html>`__ for the Raspberry Pi only supports the operating system. Below is a summary of distros released prior to ROS Noetic:

.. figure:: images/ros_distros.JPG
    :align: center
    :width: 65%

General Overview
----------------

Exchanging information with ROS can take many forms, whether it be asynchronously streaming data over topics, or using ROS services via a request/response messaging system.

.. figure:: images/ros_comm_model.png
    :align: center
    :width: 65%

ROS Master
^^^^^^^^^^

ROS master can be thought of as the main message-passing server that tracks the network addresses of all the other nodes. It informs subscribers about nodes publishing on a particular topic in order for the subscriber and publisher to establish a peer-to-peer connection. The nodes must know the location of ROS master on startup via ``ROS_MASTER_URI``, which is the enviroment variable responsible for this. Conveniently this is automatically set by default when the ROSDISTRO(noetic) setup file is sourced. For more information on sourcing setup files, refer to the ``Getting Started`` section.

Subscribers and Publishers
^^^^^^^^^^^^^^^^^^^^^^^^^^

Like previously mentioned, the subscribe/publish messaging model is one of the main ways ROS is used. For example, let's assume we have a camera on our robot and we want a way to read, process, and ouput the image feed from the camera for navigation or object tracking. To begin, the nodes must register with ROS master, this is done before the nodes can establish a peer-to-peer communication whith eachother before message-passing can occur. After registering, the Camera Node will advertise the image data to a trivial topic called ``/image_feed`` while the Image Processing Node will subscribe to ``/image_feed``.

.. figure:: images/image_feed_model.png
    :align: center
    :width: 65%
    
With Peer-to-Peer connection now established, its time for the Image Processing Node to process the incoming video stream and output to another topic called ``/image/output_video``.

.. figure:: images/image_output_model.png
    :align: center
    :width: 65%

Another subscriber can be written to view the video feed by writing a callback to the image output topic, however ROS has a framework known as rqt with many plugins like ``rqt_image_view``, that provide a GUI for displaying images using image transport.

.. note:: Refer to the RQT section for more information on the rqt GUI and its plugins.


Services and Clients
^^^^^^^^^^^^^^^^^^^^

Services/Clients are another way of passing messages, ROS services follow the basic request-response style remote procedure call (RPCs). Any node can call a service, these are referred to as clients, services are useful when a quick operation is desired. Similar to the subscriber/publisher, a ROS node provides a service under a string name that is registered with ROS Master. For example let's take a service, ``/set_motor_speed``, to set a motor speed using this, clients will send a ``request`` containing the desired motor speed value by calling the service and await an ensuing response.

.. figure:: images/motor_service_model.png
    :align: center
    :width: 65%
    


VMX-pi ROS Package
------------------
After following the steps in the Getting Started section, now you are ready to start using the ROS library for the Studica Robot Platform. ROS functionality has been implemented for a variety of Studica's hardware, refer to Studica's `Roscpp API <LINK>`__ for more information on the classes and methods available. Below are the ROS pacakages:

.. figure:: images/ros_packages.png
    :align: center
    :width: 65%
    
The next sections will go over using the ROS package to write simple subscribers and publishers, as well as writing simple services and clients to pass messages between nodes.