:mod:`socket` --- Low-level networking interface
================================================

This module provides access to the BSD socket interface. The :py:func:`socket()` function returns a socket object whose methods implement the various socket system calls. 


.. function:: socket.socket(family, type)

Create a new socket using the given address family and socket type.
 - The *address family* should be :const:`socket.AF_INET`. 
 - The *socket type* should be :const:`socket.SOCK_STREAM` or :const:`SOCK_DGRAM`
 
 
Class :class:`socket`
---------------------

.. method:: socket.connect(address)

   Connect to a remote socket at *address*. The format of *address* is: *(ipv4 address, port)*

.. method:: socket.bind(address)
   
   Bind the socket to *address*. The socket must not already be bound. The format of *address* is: *(ipv4 address, port)*

.. method:: socket.listen(backlog)
   
   Listen for connections made to the socket. The backlog argument specifies the maximum number of queued connections and should be at least 1.

.. method:: socket.accept()

   Accept a connection. The *socket* must be bound to an address and listening for connections. The return value is a pair *(conn, address)* where *conn* is a new socket object usable to send and receive data on the connection, and *address* is the address bound to the socket on the other end of the connection. The format of *address* is: *(ipv4 address, port)*

.. method:: socket.send(bytes)

   Send data to the socket. The socket must be connected to a remote socket. 

.. method:: socket.sendto(bytes, address)

   Send data to the *socket*. The *socket* should not be connected to a remote socket, since the destination socket is specified by address.  Return the number of bytes sent. The format of *address* is: *(ipv4 address, port)*

.. method:: socket.recv(bufsize)

   Receive data from the *socket*. The return value is a bytes object representing the data received. The maximum amount of data to be received at once is specified by bufsize.

.. method:: socket.recvfrom(bufsize)

   Receive data from the *socket*. The return value is a pair *(bytes, address)* where *bytes* is a bytes object representing the data received and *address* is the address of the socket sending the data.

.. method:: socket.setsockopt(level, optname, value)

   Set the value of the given socket option. The needed symbolic constants are defined in the socket module (SO_* etc.). The value can be an integer or a bytes-like object representing a buffer.

.. method:: socket.close()

   Close the *socket*. Once that happens, all future operations on the socket object will fail. The remote end will receive no more data (after queued data is flushed).


Constants
^^^^^^^^^

- Address family

  - :const:`socket.AF_INET`

- Socket types

  - :const:`socket.SOCK_STREAM`

  - :const:`socket.SOCK_DGRAM`

- Socket level

  - :const:`socket.SOL_SOCKET`

- Socket options

  - :const:`socket.SO_BROADCAST`


Examples
--------

::

  import socket

  # Echo server program
  import socket

  HOST = ''                 # Symbolic name meaning all available interfaces
  PORT = 5005               # Arbitrary non-privileged port
  
  s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
  s.bind((HOST, PORT))
  s.listen(1)
  conn, addr = s.accept()
  
  print ('Connected with ' + addr[0] + ':' + str(addr[1]))
  
  while True:
      data = conn.recv(256)
      if not data: break
      conn.send(data)
      
  conn.close()
  s.close()

::

  # Echo client program
  import socket
  
  HOST = '192.168.2.36'
  PORT = 5005
  
  s = socket.socket(socket.AF_INET, socket.SOCK_STREAM) 
  s.connect( (HOST,PORT) )
  s.send(str(val))
  data = s.recv(256)
  print('Received', repr(data))
  s.close()