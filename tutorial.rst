.. _tutorial:
   
*************************
wubby VM Tutorial
*************************

Prepare your device
========================

The first step of a **wubby**  enabled device when it starts its operation is to gain wifi connectivity so that it connects to the **wubby** Cloud. By default, the **wubby** device comes with a :file:`connectivity.cfg` file stored in the sd card. The **wubby** device owner should move the sd to his pc, edit the file, add his own wifi settings, move the sd back to the device and reboot.

The format of the :file:`connectivity.cfg` file is the following: ::

  SSID : "wubby"
  PASSPHRASE : "12345678"
  
.. note: The device will not start running the python application if the wifi settings are not correct.

Running your first script
=========================

.. In order to run a python application on *any* **wubby** enabled embedded device, you *only* need to create the :file:`main.py` file, write your application in the file and save it on the sd card of the device.

In order to run a python application on *any* **wubby** enabled embedded device, you *only* need to create the :file:`main.py` file, write your application and upload it to the device. 

.. note:: There are two ways to upload an application to a **wubby** enabled device, either through the **wubby** Client or through the **wubby** IDE. The **wubby** IDE will be available soon. Please refer to ":ref:`addApp`" and  ":ref:`installApps`" sections for detailed instructions regarding the application upload process through the **wubby** Client.


Below, you can find an example python script that demonstrates how the hardware peripherals of your **wubby** enabled device can be handled using python. 

.. code-block:: python

  # main.py -- put your code here!
  
  import time
  from hardware import adc, gpio
  import socket
  
  HOST = '192.168.2.36'
  PORT = 5005

  ## Create a new gpio object
  led = gpio(1, gpio.GPIO_MODE_OUTPUT_PULLDOWN)

  led.write(0)

  ## Create a new adc object
  analog = adc(1)

  ## Initialize the new object
  analog.init(1000)

  while True:
      ## Read Value
      val = analog.read()
      
      print ("value : " + str(val))
      
      if val > 2500:
      
          ## Write value 1 on the gpio object.
	  led.write(1)	  
	  
	  ## Connect with remote TCP server
	  s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	  s.connect( (HOST,PORT) )
	  
	  ## Send value to TCP server
	  s.send(str(val))
	  
	  ## Wait for response
	  data = s.recv(256)
	  print('Received', repr(data))
	  
	  ## Close the connection
	  s.close()
	  
      else:
	  led.write(0)
	  
      time.sleep(1000)

Open a text editor and copy the above lines. Change the HOST and PORT values to the address and port of your own running TCP server. Create a folder to save your application, eg :file:`MainApp`, and save the file as :file:`main.py`. 

.. add thermometer on the adc ----

The above application reads a value from the selected ADC peripheral and checks whether the read value is greater than a threshold. If yes, it first turns on a selected led and then connects with the remote TCP server, where it sends the read value.

As you can see, the hardware peripherals are represented as classes of the :mod:`hardware` module.

.. code-block:: python

   from hardware import adc, gpio

See :class:`hardware.gpio`, :class:`hardware.adc` for detailed description.

The first step in our application is to create the necessary objects and initialize them.
More specifically, we first create a new led, using the GPIO with pin id 1, which is defined it as `output`, and we write logical level 0 on it: ::

    ## Create a new gpio object
    led = gpio.gpio(1, gpio.GPIO_MODE_OUTPUT_PULLDOWN)

    led.write(0)
    
Then we create our analog object, using the ADC peripheral, which is initialized with sampling rate 1000: :: 

    ## Create a new adc object
    analog = adc(1)

    ## Initialize the new object
    analog.init(1000)
    
Inside the while loop we read the value of the adc and perform the rest actions of our application.


Import custom modules
=====================

For a better structure of our application, we can separate the code in several modules which are imported in our main application, as in usual python applications. The files of this modules have to be in the same directory as our :file:`main.py` file, ie under the :file:`MainApp` folder.

For example, we can create a LED class that implements the *turn on* and *turn off* operations of a led and save it as :file:`led.py`.

.. code-block:: python

  from hardware import gpio

  class LED:

	  def __init__(self, pinid, onLevel):
		  self.onLevel = onLevel
		  self.pinid = pinid
                  self.gpio = gpio(pinid, gpio.GPIO_MODE_OUTPUT_PULLDOWN)
                  self.gpio.write(1 - self.onLevel)

	  def ON(self):
                  self.gpio.write(self.onLevel)

	  def OFF(self):
                  self.gpio.write(1 - self.onLevel)

                  
Now we can modify our first example application so that the :mod:`led` is imported and used instead of accessing directly the :class:`hardware.gpio`.

.. code-block:: python

  # main.py -- put your code here!
  
  import time
  from hardware import adc
  import led
  import socket
  
  HOST = '192.168.2.36'
  PORT = 5005

  ## Create a new led object
  led1 = led.LED(1,1)

  ## Create a new adc object
  analog = adc(1)

  ## Initialize the new object
  analog.init(1000)

  while True:
      ## Read Value
      val = analog.read()
      
      print ("value : " + str(val))
      
      if val > 2500:
      
          ## Write value 1 on the gpio object.
	  led1.ON()
	  
	  ## Connect with remote TCP server
	  s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	  s.connect( (HOST,PORT) )
	  
	  ## Send value to TCP server
	  s.send(str(val))
	  
	  ## Wait for response
	  data = s.recv(256)
	  print('Received', repr(data))
	  
	  ## Close the connection
	  s.close()
	  
      else:
	  led1.OFF()
	  
      time.sleep(1000)