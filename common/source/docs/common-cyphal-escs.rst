.. _common-cyphal-escs:

=============
Cyphal ESCs
=============

Copter supports `Cyphal <https://opencyphal.org/>`__ Electronic Speed Controllers
(ESCs) that allow two-way communication with the autopilot
enabling potentially easier setup and in-flight monitoring of ESC and
motor health.

..  youtube:: 7vLIsBtI9Hs
    :width: 100%

List of CAN ESCs
================

+-----------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------+
+ Name                                                                                          + Avail                                                                                      + Ever Worked                                                                                                     +
+===============================================================================================+============================================================================================+=================================================================================================================+
+ `Holybro Kotleta20 <http://www.holybro.com/product/kotleta20/>`__                             + Yes                                                                                        + Yes                                                                                                             +
+-----------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------+
+ `Zubax Myxa <https://zubax.com/products/myxa>`__                                              + Yes                                                                                        + No                                                                                                              +
+-----------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------+

Connecting to the Flight Controller
===================================

.. image:: ../../../images/Pixhawk_UAVCAN_ESC.jpg
    :target: ../_images/Pixhawk_UAVCAN_ESC.jpg

One ESC (it does not matter which) should be connected to the flight controller's
CAN port using a 4-pin DF13 to 4-pin UCANPHY Micro (JST-GH 4) adapter cable. Each
subsequent ESC should be connected to the previous using
UCANPHY Micro (JST-GH 4) cable. The final ESC should have a CAN bus terminator plugged
into one of its UCANPHY Micro (JST-GH 4) ports.

Autopilot Setup
---------------------

Firstly, perform CAN Bus setup as described in the :ref:`CAN Bus Setup page <common-canbus-setup-advanced>`.

Then, after CAN Bus configuration is done and the autopilot is rebooted, you need to configure Autopilot Cyphal registers as described in the :ref:`Cyphal Setup page <common-cyphal-setup-advanced>`.


ESC setup using yakut
===================================

ESC might be configured via CAN bus using the `Yakut` tool.
Please, check the `Yakut github page <https://github.com/OpenCyphal/yakut>`__  to to be familiar with it.
The Cyphal registers configuration process is common for any Cyphal devices and is described in the :ref:`Cyphal Setup page <common-cyphal-setup-advanced>`.

Logging and Reporting
---------------------

Cyphal ESCs provide information back to the autopilot which is recorded in the autopilot's onboard log's CESC messages and can be viewed in any :ref:`ArduPilot compatible log viewer <common-logs>`.  This information includes:

1. Error Count
2. Voltage
3. Current
4. Temperature
5. RPM
6. Power (as a percentage)

The RCOU messages are also written to the onboard logs which hold the requested output level sent to the ESCs expressed as a number from 1000 (meaning stopped) to 2000 (meaning full output).

Additional information
======================

1. `yakut github page <https://github.com/OpenCyphal/yakut>`__,
2. `public regulated data types page <https://github.com/OpenCyphal/public_regulated_data_types>`__,
3. `Zubax Sapog v3 page <https://github.com/Zubax/sapog/tree/v3>`__.
