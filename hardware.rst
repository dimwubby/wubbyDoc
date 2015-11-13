:mod:`hardware` --- The Hardware module API
=============================================

.. module:: hardware

The module described in this chapter provides classes that handle several hardware peripherals.


Class GPIO
-----------

The :class:`gpio` class controls the GPIO peripheral. It has methods to set the mode of the GPIO and methods to get
and set the digital logic level.
    
.. class:: gpio(pin, mode)

 Return a `gpio` object mapped to the given `pin` argument and initialize it according to the `mode` argument.
  
 `mode` can be one of the following:
  
  * :const:`GPIO_MODE_INPUT_PULLUP` : configure the pin as input, enable the pull-up resistor.
  * :const:`GPIO_MODE_INPUT_PULLDOWN`: configure the pin as input, enable the pull-down resistor.
  * :const:`GPIO_MODE_OUTPUT_PULLUP`: configure the pin as output, enable the pull-up resistor.
  * :const:`GPIO_MODE_OUTPUT_PULLDOWN`: configure the pin as output, enable the pull-down resistor.
  
 A *ValueError* exception is thrown if a GPIO with the given pin does not exist .
  
 The :class:`gpio` class provides the following methods. All of them raise a *ValueError* exception if the operation
 was not successfull.
  
.. method:: gpio.write( level )

  Set the selected GPIO to the value "0" or "1". 
  Logic 1 puts a 3.3V signal on the GPIO while
  Logic 0 puts a 0V signal on the GPIO.

.. method:: gpio.read()

  Return "0" or "1" depending on the state of the GPIO.
  

.. method:: gpio.toggle()

  Check the current status of the GPIO pin and toggle the output to 
  the opposite state that is currently in. The return value is the current state of the GPIO pin.
  
  
  Example usage: ::

    import time
    from hardware import gpio

    print ("Wubby Project")
    print ("GPIO Example")

    ## Create a new gpio object
    led = gpio(2,gpio.GPIO_MODE_OUTPUT_PULLDOWN)
    
    led.write(0)

    ## Read Value
    while True:
	    led.toggle()
	    time.sleep(1000)
  
Class ADC
---------

The :class:`adc` class controls the ADC peripheral. More specifically it associates an `adc` object with a GPIO pin and 
provides the methods to read analog values on that pin.
   
.. class:: adc(pin)

   Return an `adc` object mapped to the given `pin`.
   
 The :class:`adc` class provides the following methods.
 
.. method:: adc.init(sampling_rate)
   
   Initialize the :class:`adc` object with the specified sampling rate. This is the first method that should be called after the creation of the object so that the ADC peripheral is correctly initialized.
   :py:func:`init` throws a *ValueError* exception
   if the *adc* object could not be initialized.
   
   
.. method:: adc.read()
   
   Read the value on the analog pin and return it. 
   
   Example usage: ::

    import time
    from hardware import adc

    print ("Wubby Project")
    print ("ADC Example")

    ## Create a new adc object
    analog = adc(1)


    ## Initialize the new object
    analog.init(1000)


    ## Read Value
    while True:
	    val = analog.read()
	    print ("value : " + str(val))
	    time.sleep(1000)
   
   
Class UART
----------

The :class:`uart` class implements the UART serial communication bus and provides methods to read and write through it.
   
.. class:: uart(bus)

   Return a `uart` object mapped to the given `bus`.

.. method:: uart.init( baudrate, [parity, twoStopBits, timeout, bufferLen])

   Initialize the :class:`uart` object with the given parameters. 
   
   - *baudrate* is the baudrate of the UART.
   - *parity* can be 0 (no parity), 1 (odd) or 2 (even). If no *parity* is given, the object is initialized with parity = 0.
   - *twoStopBits* can be :const:`True` or :const:`False`. Default value is :const:`False`.
   - *timeout* is the timeout in milliseconds to wait for the first character.
   - *bufferLen* is the character length of the read buffer.
   
   This is the first method that should be called after the creation of the object so that the UART peripheral is correctly initialized.
   :py:func:`init` throws a *ValueError* exception if the *uart* object could not be initialized.

The :class:`uart` class provides the following methods.
   
.. method:: uart.write(buf)

   Write the buffer of bytes *buf* to the bus and return the number of bytes written.

.. method:: uart.read()

   Read characters until a timeout happens or *\\n* termination character is received.

   
Class SPI
---------

The :class:`spi` class implements the SPI serial interface. 

.. class:: spi(id)

 Return an `spi` object. *id* is the identifier of the selected SPI interface. 
  
The :class:`spi` class provides the following methods.  
   
.. method:: spi.init( clock, mode, ss)

  Initialize the `spi` object with the given parameters. Where:
   
   - *clock* - the SCK clock rate (only sensible for a master).
   - *mode* - the clock polarity (CPOL) and phase (CPHA). In addition to setting the clock frequency, the master must also configure the clock polarity and   phase with respect to the data. Possible values are:
      - 0: CPOL=0, CPHA=0
      - 1: CPOL=0, CPHA=1
      - 2: CPOL=1, CPHA=0
      - 3: CPOL=1, CPHA=1
      
   - *ss* - the GPIO pin used as the *slave select* signal.
   
  This is the first method that should be called after the creation of the object so that the SPI peripheral is correctly initialized.
  
.. method:: spi.deinit()
   
   Deinitialize the *spi* object.

.. method:: spi.read(recv, timeout=5000)

   Receive data on the bus.
   
   - *recv* - can be an integer, which is the number of bytes to receive, or a mutable buffer, which will be filled with received bytes.
   - *timeout* - the timeout in milliseconds to wait for the receive.
   
.. method:: spi.write(end, timeout=5000)

   Write data on the bus.
   
   - *send* is the data to send (an integer to send, or a buffer object).
   
   - *timeout* is the timeout in milliseconds to wait for the send.
   
  
Class PWM
----------

The :class:`pwm` class implements the PWM peripheral.

.. class:: pwm(id)

   Return a `pwm` object. *id* is the identifier of the selected PWM peripheral. 
   
.. method:: pwm.init(frequency, duty_cycle)

   Initialize the :class:`pwm` object with the given parameters. Where:
   
   - *frequency*  is the PWM's switching frequency.
   - *duty_cycle* is the proportion of 'on' time to the regular interval or 'period' of time; a low duty cycle corresponds to low power, because the power is off for most of the time. Duty cycle is expressed in percent, 100% being fully on.
   
   This is the first method that should be called after the creation of the object so that the PWM peripheral is correctly initialized.
   
   The :py:func:`init` method throws a *ValueError* exception if the peripheral with the given id is not found.
   
.. method:: pwm.start()

   Start the operation of the *pwm* .
   
.. method:: pwm.stop()
  
   Stop the operation of the *pwm*.

   
   Example usage: ::
   
    import time                                                                    
    from hardware import pwm

    print ("Wubby Project")                                                
    print ("PWM Example")                                                          

    ## Create a new adc object                                                     
    fade = pwm(1)                                                                 


    ## Initialize the new object                                                   

    while True:
	    fade.init(1000,1)
	    fade.start()
	    time.sleep(10000)
	    fade.stop()
	    fade.init(1000,30)
	    fade.start()
	    time.sleep(10000)
	    fade.stop()
	    time.sleep(10000)

   
	














