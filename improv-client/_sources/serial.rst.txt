Serial transport
================

Install with ``pip install improv-client[serial]``.

The device must run Improv on its serial port, which ESPHome does on the USB
serial port by default (``improv_serial:``). Improv frames share the port with
the device log; the client skips everything that isn't a valid frame.

The serial client has two commands BLE doesn't have:
:meth:`~improv_client.serial.ImprovSerialClient.request_current_state` and
:meth:`~improv_client.serial.ImprovSerialClient.get_network_state`. It has no
``identify``.

.. automodule:: improv_client.serial
   :members:
   :show-inheritance:
   :inherited-members:
