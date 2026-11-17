Python Improv Client
====================

|PyPI version| |PyPI pyversions| |PyPI license|

Python library that implements an `Improv
Wi-Fi <https://www.improv-wifi.com/>`__ client, for provisioning Wi-Fi
credentials to devices over serial and Bluetooth Low Energy (BLE).

Full documentation: https://jonnybergdahl.github.io/improv-client/

Features
--------

- Async (``asyncio``) clients for both transports: serial
  (pyserial-asyncio) and BLE (bleak with bleak-retry-connector)
- The same API on both: ``provision``, ``get_device_info``,
  ``scan_wifi``, ``get_hostname``/``set_hostname``,
  ``get_device_name``/``set_device_name``, ``wait_for_state``, plus
  state and error callbacks
- Serial only: ``request_current_state`` and ``get_network_state``
- BLE only: ``identify``, discovery with advertised state and
  capabilities, connect on demand and disconnect when idle
- Handles device info with OS name and version (ESPHome 2025.12 and
  later)
- Results come back as dataclasses with ``str`` fields, and device
  errors as exceptions
- A protocol layer with no dependencies, for writing other transports
- An ``improv-client`` command line tool

Installation
------------

Install with the extra for the transport you need:

.. code:: shell

   pip install improv-client[serial]   # serial only
   pip install improv-client[ble]      # BLE only
   pip install improv-client[all]      # both

Python 3.10 or newer is required.

Usage
-----

Serial
~~~~~~

.. code:: python

   import asyncio
   from improv_client.serial import ImprovSerialClient


   async def main():
       async with ImprovSerialClient("/dev/ttyUSB0") as client:
           print("State:", (await client.request_current_state()).name)

           info = await client.get_device_info()
           print(info.firmware_name, info.firmware_version, info.device_name)

           for network in await client.scan_wifi():
               print(network.ssid, network.rssi, network.secured)

           url = await client.provision("MyWirelessAP", "mysecurepassword")
           print("Provisioned, next step:", url)


   asyncio.run(main())

BLE
~~~

.. code:: python

   import asyncio
   from improv_client import State
   from improv_client.ble import ImprovBleClient, discover


   async def main():
       devices = await discover()
       if not devices:
           print("No Improv devices found")
           return

       async with ImprovBleClient(devices[0].device) as client:
           if client.state is State.AUTHORIZATION_REQUIRED:
               print("Press the button on the device")
               await client.wait_for_state(State.AUTHORIZED, timeout=60)

           url = await client.provision("MyWirelessAP", "mysecurepassword")
           print("Provisioned, next step:", url)


   asyncio.run(main())

The BLE client also connects by itself when you call a command, and
disconnects after 90 seconds without commands. Pass
``idle_disconnect_delay=None`` to stay connected.

Errors
~~~~~~

Every exception derives from ``ImprovError``:

+-----------------------------+-----------------------------------------------------+
| Exception                   | Raised when                                         |
+=============================+=====================================================+
| ``ImprovDeviceError``       | The device reported an error,                       |
|                             | e.g. ``Error.UNABLE_TO_CONNECT`` for wrong          |
|                             | credentials                                         |
+-----------------------------+-----------------------------------------------------+
| ``ImprovNotSupportedError`` | The device doesn’t support the command              |
+-----------------------------+-----------------------------------------------------+
| ``ImprovTimeoutError``      | The device didn’t answer in time (also a            |
|                             | ``TimeoutError``)                                   |
+-----------------------------+-----------------------------------------------------+
| ``ImprovConnectionError``   | Connecting failed or the connection was lost        |
+-----------------------------+-----------------------------------------------------+
| ``ImprovProtocolError``     | The device sent data that couldn’t be parsed        |
+-----------------------------+-----------------------------------------------------+

Command line tool
-----------------

.. code:: shell

   improv-client discover
   improv-client serial /dev/ttyUSB0 state
   improv-client serial /dev/ttyUSB0 info
   improv-client serial /dev/ttyUSB0 network
   improv-client serial /dev/ttyUSB0 provision MyWirelessAP   # prompts for the password
   improv-client ble AA:BB:CC:DD:EE:FF scan
   improv-client ble AA:BB:CC:DD:EE:FF hostname new-hostname
   improv-client ble AA:BB:CC:DD:EE:FF identify

On macOS, BLE addresses are UUIDs. Use the one ``discover`` prints. Add
``-v`` to log the protocol traffic.

Development
-----------

.. code:: shell

   pip install -e .[dev]
   pytest
   ./build.sh   # builds the docs

Credits
-------

The BLE client borrows design ideas from
`py-improv-ble-client <https://github.com/home-assistant-libs/py-improv-ble-client>`__
(MIT): the retrying connector, disconnecting when idle, and matching
results to commands by command id.

.. |PyPI version| image:: https://badge.fury.io/py/improv-client.svg
   :target: https://badge.fury.io/py/improv-client
.. |PyPI pyversions| image:: https://img.shields.io/pypi/pyversions/improv-client.svg
   :target: https://pypi.python.org/pypi/improv-client/
.. |PyPI license| image:: https://img.shields.io/pypi/l/improv-client.svg
   :target: https://pypi.python.org/pypi/improv-client/
