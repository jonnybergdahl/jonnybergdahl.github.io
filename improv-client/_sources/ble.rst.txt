BLE transport
=============

Install with ``pip install improv-client[ble]``.

Find devices with :func:`~improv_client.ble.discover`, or pass a
:class:`bleak.backends.device.BLEDevice` from your own scanner (for example the
Home Assistant Bluetooth integration) to :class:`~improv_client.ble.ImprovBleClient`.

Devices may start in :attr:`~improv_client.protocol.State.AUTHORIZATION_REQUIRED`
and need a button press before they accept credentials. Wait for that with
:meth:`~improv_client.client.ImprovClient.wait_for_state`.

Commands that the device's capabilities don't include raise
:class:`~improv_client.exceptions.ImprovNotSupportedError` without being sent.
The device name commands have no capability bit and are always sent.

.. automodule:: improv_client.ble
   :members:
   :show-inheritance:
   :inherited-members:
