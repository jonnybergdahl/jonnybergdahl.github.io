Protocol
========

Everything here is transport independent and has no third-party dependencies. The
clients use it internally; you need it for the enums and result types, or to
implement another transport.

Notes the specification leaves implicit:

- The serial checksum is the sum of every frame byte from ``IMPROV`` through the
  data, keeping the low byte. Clients write a newline after each frame.
- The RPC payload inside a serial frame has no checksum of its own. On BLE, the
  checksum is appended to the RPC payload.
- On serial, a Wi-Fi scan returns one result per network, then an empty result.
  On BLE it returns one result with three strings per network.

.. automodule:: improv_client.protocol
   :members:
   :show-inheritance:
