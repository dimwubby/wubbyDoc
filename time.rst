:mod:`time` --- Time related functions
=============================================

.. module:: time

The :mod:`time` module provides functions for getting the current time and for sleeping.

Functions
---------

.. function:: time.time()

   Return the time as a floating point number expressed in seconds since the epoch.
   
.. function:: time.sleep(msec)

   Suspend execution of the current thread for the given number of seconds.
   
   ::
   
    # Print "Alive!" every second
    import time
    
    while True:
      print ("Alive!")
      time.sleep(1000)
      
