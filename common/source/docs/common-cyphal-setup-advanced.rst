.. _common-cyphal-setup-advanced:

==============
Cyphal Setup
==============

This article provides guidance to setup Cyphal protocol on ArduPilot.

.. tip::

   The physical CAN port and its driver selected as Cyphal protocol should be enabled first. Please refer to the
   :ref:`CAN Bus Setup <common-canbus-setup-advanced>`

Overview
========

Cyphal is an open technology for real-time intravehicular distributed computing
and communication based on modern networking standards (Ethernet, CAN FD,
etc.). It was created to address the challenge of on-board deterministic
computing and data distribution in next-generation intelligent vehicles: manned
and unmanned aircraft, spacecraft, robots, and cars.

Detailed description of protocol can be found at https://opencyphal.org/

Cyphal Peripheral Types Supported
===================================

ArduPilot currently officially doesn't support the Cyphal protocol, but it may appear a bit further.
Check `the pull-request #20408 <https://github.com/ArduPilot/ardupilot/pull/20408>`__ for status.

Despite this the following periphery might be run on the custom Ardupilot firmware.

Inside `the pull-request branch <https://github.com/PonomarevDA/ardupilot/tree/pr-uavcan-v1>`__ (tested on the real UAV):

1. ESC

Inside `the test branch <https://github.com/PonomarevDA/ardupilot/tree/pr-uavcan-v1-hitl>`__ (tested in the HITL simulator yet only):

1. Gps
2. Magnetometer
3. Barometer
4. Gyroscope+Accelerometer (as part of HITL simulation only)

Cyphal Registers Configuration
=============================================

Any Cyphal devices including Ardupilot should support `uavcan.register.List` and `uavcan.register.Access` that make it able to be configured via `Yakut` cli tool.

Typically, you need to connect your devices via CAN-sniffer to your PC and perform the following steps:

1. Setup the environment (bring CAN interface up, compile DSDL and set environment variables)
2. Perform plug and play procedure to automatically setup node identifiers if they are not configured yet. Don't forget to save the new identifiers and reboot the devices.
3. Configure ESC Cyphal registers using `uavcan.register.List`` and `uavcan.register.Access`` RPC-services.
4. Configure other device registers (such as TTL timeout for ESC) if needed.

An example of Ardupilot and ESC configuration might be found here: https://github.com/PonomarevDA/cyphal_configurator

Cyphal ESC and Servo Configuration settings
=============================================

The Cyphal drone application layer (UDRAL) specification defines the interface for ESCs.

According to the specification we have 2 required output subjects (setpoint and readiness) and few
optional input subjects (feedback, status, power, dynamics). It also my have note_response subject.

All subjects might be configured via Ardupilot parameters.

The list of Ardupilot registers and corresponded parameters shown in the table below:

+-----------------+--------------+-----------------------------------------------------------+
|Parameter        |Register name |Description                                                |
+-----------------+--------------+-----------------------------------------------------------+
|CAN_D1_UC1_SP    |setpoint      |reg.udral.service.actuator.common.sp.*                     |
+-----------------+--------------+-----------------------------------------------------------+
|CAN_D1_UC1_RD    |readiness     |reg.udral.service.common.Readiness                         |
+-----------------+--------------+-----------------------------------------------------------+
|CAN_D1_UC1_NOTE  |note_response |reg.udral.physics.acoustics.Note                           |
+-----------------+--------------+-----------------------------------------------------------+
|CAN_D1_UC1_FB<N> |feedback<N>   |reg.udral.service.actuator.common.Feedback                 |
+-----------------+--------------+-----------------------------------------------------------+
|CAN_D1_UC1_FB<N> |status<N>     |reg.udral.service.actuator.common.Status                   |
+-----------------+--------------+-----------------------------------------------------------+
|CAN_D1_UC1_POW<N>|power<N>      |reg.udral.physics.electricity.PowerTs                      |
+-----------------+--------------+-----------------------------------------------------------+
|CAN_D1_UC1_DYN<N>|dynamics<N>   |reg.udral.physics.dynamics.rotation.PlanarTs_0_1           |
+-----------------+--------------+-----------------------------------------------------------+

Here `N` is the number of motor. For a quadcopter these numbers will be 1, 2, 3 and 4.

.. note::

   At this moment, only quadcopter is supported, so the data type of setpoint is always reg.udral.service.actuator.common.sp.Vector4.


Cyphal GPS configuration settings
=============================================

If there is a Cyphal GPS device, it has to be enabled in ``GPS``
subgroup of parameters.
The ``TYPE`` parameter should be set to 24 for corresponding GPS receiver in autopilot.

The list of Ardupilot registers and corresponded parameters for GPS are shown in the table below:

+----------------+--------------+-----------------------------------------------------------+
|Parameter       |Register name |Description                                                |
+----------------+--------------+-----------------------------------------------------------+
|CAN_D1_UC1_GPO  |gps_point     |reg.udral.physics.kinematics.geodetic.PointStateVarTs.0.1  |
+----------------+--------------+-----------------------------------------------------------+
|CAN_D1_UC1_GPY  |gps_yaw       |uavcan.si.sample.angle.Scalar                              |
+----------------+--------------+-----------------------------------------------------------+

.. note::

   At this moment, the implementation has only gps_point.
   Yaw angle is manually estimated using NED velocity.
   Number of satellites and vdop, hdop is always expected as ok.
   Obviously, the GPS implementation should be extended ASAP. 


Cyphal Barometer configuration settings
=============================================

The list of Ardupilot registers and corresponded parameters for Barometer shown in the table below:

+----------------+--------------+-----------------------------------------------------------+
|Parameter       |Register name |Description                                                |
+----------------+--------------+-----------------------------------------------------------+
|CAN_D1_UC1_BPR  |baro_pressure |uavcan.si.sample.pressure.Scalar.1.0                       |
+----------------+--------------+-----------------------------------------------------------+
|CAN_D1_UC1_BTE  |baro_temp     |uavcan.si.sample.temperature.Scalar.1.0                    |
+----------------+--------------+-----------------------------------------------------------+


Cyphal Magnetometer configuration settings
=============================================

The list of Ardupilot registers and corresponded parameters for Magnetometer shown in the table below:

+----------------+--------------+-----------------------------------------------------------+
|Parameter       |Register name |Description                                                |
+----------------+--------------+-----------------------------------------------------------+
|CAN_D1_UC1_MAG  |mag           |uavcan.si.sample.magnetic_field_strength.Vector3.1.0       |
+----------------+--------------+-----------------------------------------------------------+

Cyphal IMU configuration settings
=============================================

The list of Ardupilot registers and corresponded parameters for IMU shown in the table below:

+----------------+--------------+-----------------------------------------------------------+
|Parameter       |Register name |Description                                                |
+----------------+--------------+-----------------------------------------------------------+
|CAN_D1_UC1_ACC  |accel         |uavcan.si.sample.acceleration.Vector3.1.0                  |
+----------------+--------------+-----------------------------------------------------------+
|CAN_D1_UC1_GYR  |gyro          |uavcan.si.sample.angular_velocity.Vector3.1.0              |
+----------------+--------------+-----------------------------------------------------------+

.. note::

   It is expected to use IMU only in HITL mode.
   Since HITL mode requires to disable all internal board sensors and Ardupilot can't be
   initialized without IMU at all, so as a temporary solution it was decided to implement dummy
   Cyphal IMU if the real one is not exist.

Cyphal useful utilities
=============================================

1. https://github.com/OpenCyphal/yakut
2. https://github.com/OpenCyphal/public_regulated_data_types
