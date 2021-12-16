Ultrasonic Distance Sensor
==========================

The Ultrasonic Distance Sensor has been updated from a 3-pin to a 4-pin sensor. This was done to create better compatiblity between multiple different control systems. 

.. figure:: images/ultrasonic-1.jpg
    :align: center

.. list-table:: Electrical Characteristics
    :widths: 30 10 10 10
    :header-rows: 1
    :align: center
   
    *  - Function
       - Min
       - Nom
       - Max
    *  - Input Voltage
       - ---
       - ---
       - 5VDC
    *  - Current
       - ---
       - 15mA
       - ---
    *  - Range
       - 2cm
       - ---
       - 400cm
    *  - Measure Angle
       - ---
       - 15°
       - ---
    *  - Frequency
       - ---
       - 40Hz
       - ---
    *  - Trigger Pulse
       - ---
       - 10μS TTL 
       - ---

Programming the Ultrasonic Distance Sensor
------------------------------------------

.. tabs::
   
    .. tab:: Java

        .. code-block:: java
            :linenos:

            //import the Ultrasonic Library
            import edu.wpi.first.wpilibj.Ultrasonic;

            //Create the Ultrasonic Object
            private Ultrasonic sonar;

            //Constuct a new instance
            sonar = new Ultrasonic(Trigger, Echo);

            //Create an accessor method
            public double getDistance()
            {
                return sonar.getRangeInches();
                // or can use 
                return sonar.getRangeMM();
            }
    
        The accessor methods will then output the range in either inches or mm.

        .. note:: The valid digital pairs for Trigger and Echo pins are (Trigger, Echo) ``(0,1)``, ``(2,3)``, ``(4,5)``, ``(6,7)``, ``(8, 9)``, ``(10,11)``

    .. tab:: C++

        .. code-block:: c++
            :linenos:

            //Include the Ultrasonic Library
            #include "frc/Ultrasonic.h"

            //Constructors
            frc::Ultrasonic sonar{Trigger, Echo};

            //Create an accessor function
            double getDistance(void)
            {
                return sonar.GetRangeInches();
                // or can use 
                return sonar.GetRangeMM();
            }

        The accessor functions will then output the range in either inches or mm.  

        .. note:: The valid digital pairs for Trigger and Echo pins are (Trigger, Echo) ``(0,1)``, ``(2,3)``, ``(4,5)``, ``(6,7)``, ``(8, 9)``, ``(10,11)``
     
    .. tab:: Roscpp
     
        .. code-block:: c++
            :linenos:
            
            //Include the Ultrasonic Library
            #include "Ultrasonic_ros.h"
            
            
            double ultrasonic_cm;
            
            // Returns the distance value reported by the Ultrasonic Distance sensor
            void ultrasonic_cm_callback(const std_msgs::Float32::ConstPtr& msg)
            {
               ultrasonic_cm = msg->data;
            }
            
            int main(int argc, char **argv)
            {
               system("/usr/local/frc/bin/frcKillRobot.sh"); //Terminal call to kill the robot manager used for WPILib before running the executable.
               ros::init(argc, argv, "ultrasonic_node");
               
               /**
                * Constructor
                * Ultrasonic's ros threads (publishers and services) will run asynchronously in the background
                */
                
               ros::NodeHandle nh; //internal reference to the ROS node that the program will use to interact with the ROS system
               VMXPi vmx(true, (uint8_t)50); //realtime bool and the update rate to use for the VMXPi AHRS/IMU interface, default is 50hz within a valid range of 4-200Hz
               
               ros::Subscriber ultrasonicCM_sub;
               
               UltrasonicROS ultrasonic(&nh, &vmx, 8, 9); //channel_index_out(8), channel_index_in(9)
               ultrasonic.Ultrasonic(); //Sends an ultrasonic pulse for the ultrasonic object to read
                              
               // Use these to directly access data
               uint32_t raw_distance = ultrasonic.GetRawValue(); // returns distance in microseconds
               // or can use
               uint32_t cm_distance = ultrasonic.GetDistanceCM(raw_distance); //converts microsecond distance from GetRawValue() to CM
               // or can use
               uint32_t inch_distance = ultrasonic.GetDistanceIN(raw_distance); //converts microsecond distance from GetRawValue() to IN
                    
               // Subscribing to Ultrasonic distance topic to access the distance data
               ultrasonicCM_sub = nh.subscribe("channel/9/ultrasonic/dist/cm", 1, ultrasonic_cm_callback); //This is subscribing to channel 9, which is the input channel set in the constructor
               
               ros::spin(); //ros::spin() will enter a loop, pumping callbacks to obtain the latest sensor data
               
               return 0;
            }
         
        The accessor functions will then output the range in either microseconds, inches, or cm.  

        .. important:: The valid digital pairs for Trigger and Echo pins are (Trigger, Echo) ``(0,1)``, ``(2,3)``, ``(4,5)``, ``(6,7)``, ``(8, 9)``, ``(10,11)``. Subscribe to Ultrasonic topics to access the data being published and write callbacks to pass messages between various processes.
        
        .. note:: Calling the ``frcKillRobot.sh`` script is necessary since the VMXPi HAL uses the pigpio library, which unfortunately can only be used in one process. Thus, everything that interfaces with the VMXPi must be run on the same executable. For more information on programming with ROS, refer to: `ROS Tutorials <http://wiki.ros.org/ROS/Tutorials>`__.