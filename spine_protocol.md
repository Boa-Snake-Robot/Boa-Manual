# Spine Protocol Specification

## Introduction

The arbitration ID is separated into 2 parts 

Bits 0 through 5 represent the Link ID the message is intended for with a value from 0 to 64. 

Bits 6 through 10 of the arbitration ID represent the OpCode, with a value from 0 to 31

Because of the way CAN works, the OpCode will also determine the priority of the messages. A message with a low OpCode will take arbitration over a message with a high OpCode. This is why the safety critical messages such as error reporting and control messages have low OpCodes, and low priority messages such as configuration messages have high OpCodes.  

If a link is transmitting the message, then the link number represents the link number of the link the message originated from. If the head link is transmitting a message, then the link number represents the link that is targeted by the message. A link number of 0 signifies a broadcast with no intended target, such as the `SYNC SENSOR` message.

The messages are divided into 4 types based on their OpCode:

- `0x01 -> 0x04`: Emergency messages
- `0x05 -> 0x06`: Sync Messages
- `0x07 -> 0x09`: Control messages 
- `0x0B -> 0x12`: Sensor messages
- `0x14 -> 0x17`: Config messages


## OpCodes

| OpCode | Name              | Use                                                                                                                            |
| ------ | ----------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| `0x00` |                   |                                                                                                                                |
| `0x01` | PANIC             | Immediately stop all motors. Do not accept any further control messages until reboot                                           |
| `0x02` | FATAL ERROR       | An error has occurred that prevents normal function                                                                            |
| `0x03` | WARNING           | An event has occurred that should not normally be happening                                                                    |
| `0x04` |                   |                                                                                                                                |
| `0x05` | SYNC SENSOR       | A notifier message to a link (or all links) to send its configured sensor measurements onto the CAN bus                        |
| `0x06` | SYNC CONTROL      | Causes all links to enact the latest control message received from the head link.                                              |
| `0x07` | SET JOINT ANGLE   | Sets the reference angle for position control. Ignored if the control mode of the link is not set to ANGLE                     |
| `0x08` | SET JOINT ANGVEL  | Sets the reference angular velocity for angular velocity control. Ignored if the control mode of the link is not set to ANGVEL |
| `0x09` | SET JOINT TORQUE  | Sets the reference torque for torque control. Ignored if the control mode of the link is not set to TORQUE                     |
| `0x0A` |                   |                                                                                                                                |
| `0x0B` | SENSOR LINK ACC   | IMU Measurement of link acceleration in local frame                                                                            |
| `0x0C` | SENSOR LINK IMU   | IMU Measurement of link angle (global) and angular velocity (global)                                                           |
| `0x0D` | SENSOR LINK SERVO | Measurements from the servo including joint torque, joint angle, joint angular velocity and motor temperature                  |
| `0x0E` | SENSOR LINK CFS   | Measurements from the CFS                                                                                                      |

## Description of Spine Protocol Messages

### PANIC

A panic message will immediately halt the servos to prevent damage to the system in case that something goes horribly wrong. After a `PANIC` message, the servos will not restart until the power has been switched off and on again. 

The payload of the message is disregarded

A `PANIC` message is always broadcast with link ID 0

### FATAL ERROR

An error has occured in one of the links which prevents it from working as intended. In the case that the error incurs a safety risk, a `FATAL ERROR` message should be immediately replied to with a `PANIC` message from the brain

The payload contains 1 byte

| Byte | Type      | Content                                        |
| ---- | --------- | ---------------------------------------------- |
| `0`  | `uint8_t` | The Error Code of the error that just happened |


### WARNING

An event has occured in one of the links which does not prevent it from working as intended, but should be addressed before normal operation.

The payload contains 1 byte

| Byte | Type      | Content                                            |
| ---- | --------- | -------------------------------------------------- |
| `0`  | `uint8_t` | The Warning Code of the warning that just happened |


### SYNC SENSOR

The `SYNC SENSOR` message is emitted from the brain at regular intervals to signal to the links that they should send the latest
sensor data onto the spine bus. When this message is emitted, the links should reply _as quickly as possible_ with the latest sensor values.
This will incur a lot of congestion on the spine bus, but it is also the fastest way of getting synchronized sensor data from all the links
at the same time.

A `SYNC SENSOR` message is always broadcast with link ID 0

The payload contains 1 byte

| Byte | Type      | Content     |
| ---- | --------- | ----------- |
| `0`  | `uint8_t` | Empty (0x0) |

### SYNC CONTROL

The `SYNC CONTROL` message is emitted from the brain to tell the links to enact the latest control message they received. Any control messages
received by a link should be deferred (and stored) when received, and only take effect upon the reception of a `SYNC SENSOR` message. 
When this message is emitted, the links should enact the latest control message _as quickly as possible_. Using this form of synchronization makes it 
possible for all links to perform a coordinated movement without any delay between the actuation in each link

A `SYNC SENSOR` message is always broadcast with link ID 0

The payload contains 1 byte

| Byte | Type      | Content     |
| ---- | --------- | ----------- |
| `0`  | `uint8_t` | Empty (0x0) |


### SYNC CONTROL

The `SYNC CONTROL` message is emitted from the brain to tell the links to enact the latest control message they recieved. Any control messages
recieved by a link should be deferred (and stored) when recieved, and only take effect upon the reception of a `SYNC SENSOR` message. 
When this message is emitted, the links should enact the latest control message _as quickly as possible_. Using this form of synchronization makes it 
possible for all links to perform a coordinated movement without any delay between the actuation in each link

A `SYNC SENSOR` message is always broadcast with link ID 0

The payload contains 1 byte

| Byte | Type      | Content     |
| ---- | --------- | ----------- |
| `0`  | `uint8_t` | Empty (0x0) |


### SET JOINT ANGLE

The `SET JOINT ANGLE` message is emitted from the brain to set the target joint angle of the servo in a given link. If the link is not currently in 
angle control mode (i.e. torque control og speed control), the link is immediately switched to angle control mode upon the reception of this message.
The message should not be enacted before the reception of a `SYNC CONTROL` message.

The payload contains 4 bytes

| Byte  | Type      | Content                                |
| ----- | --------- | -------------------------------------- |
| `0-3` | `int32_t` | Target joint angle **TODO: Add units** |


### SET JOINT ANGVEL

The `SET JOINT ANGVEL` message is emitted from the brain to set the target joint angular velocity of the servo in a given link. If the link is not currently in 
angular velocity control mode (i.e. torque control og angle control), the link is immediately switched to angular velocity control mode upon the reception of this message.
The message should not be enacted before the reception of a `SYNC CONTROL` message.

The payload contains 4 bytes

| Byte  | Type      | Content                                           |
| ----- | --------- | ------------------------------------------------- |
| `0-3` | `int32_t` | Target joint angular velocity **TODO: Add units** |


### SET JOINT TORQUE

The `SET JOINT TORQUE` message is emitted from the brain to set the target joint torque of the servo in a given link. If the link is not currently in 
torque control mode (i.e. angular velocity control og angle control), the link is immediately switched to torque control mode upon the reception of this message.
The message should not be enacted before the reception of a `SYNC CONTROL` message.

The payload contains 2 bytes

| Byte  | Type      | Content                           |
| ----- | --------- | --------------------------------- |
| `0-1` | `int16_t` | Target torque **TODO: Add units** |


### SENSOR LINK ACC

The `SENSOR LINK ACC` message is emitted from a link as a response to the `SYNC SENSORS` message. The message contains the latest acceleration measurements
from the IMU in a link. 

The payload contains 8 bytes

| Byte  | Type    | Content                        |
| ----- | ------- | ------------------------------ |
| `0-3` | `float` | X-axis acceleration in `m/s^2` |
| `4-7` | `float` | Y-axis acceleration in `m/s^2` |


### SENSOR LINK GYRO

The `SENSOR LINK GYRO` message is emitted from a link as a response to the `SYNC SENSORS` message. The message contains the latest gyro measurements
from a link. 

The payload contains 8 bytes

| Byte  | Type    | Content                                     |
| ----- | ------- | ------------------------------------------- |
| `0-3` | `float` | Link angular velocity in degrees per second |
| `4-7` | `float` | Link angle in degrees                       |


### SENSOR LINK SERVO

The `SENSOR LINK SERVO` message is emitted from a link as a response to the `SYNC SENSORS` message. The message contains the latest measurements
from the on-board sensors in the servo of a link. 

The payload contains 7 bytes

| Byte  | Type       | Content                                     |
| ----- | ---------- | ------------------------------------------- |
| `0-1` | `int16_t`  | Joint torque current **TODO: add units**    |
| `2-3` | `int16_t`  | Joint angular velocity  **TODO: add units** |
| `4-5` | `uint16_t` | Joint angle  **TODO: add units**            |
| `6`   | `int8_t`   | Motor temperature in degrees Celsius        |



### SENSOR LINK CFS

The `SENSOR LINK CFS` message is emitted from a link as a response to the `SYNC SENSORS` message.
The message contains the latest constraint force measurements from a link

The payload contains 8 bytes

| Byte  | Type      | Content                                                       |
| ----- | --------- | ------------------------------------------------------------- |
| `0-3` | `int32_t` | Constraint force along the local X-axis  **TODO: Add units**  |
| `4-7` | `int32_t` | Constraint force along the local Y-axis   **TODO: Add units** |