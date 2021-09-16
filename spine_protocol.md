# Spine Protocol Specification

The arbitration ID is separated into 2 parts 

Bits 0 through 4 of the arbitration ID represent the OpCode, with a value from 0 to 31

Bits 5 through 10 represent the Link number the message is intended for with a value from 0 to 64. If a link is transmitting the message, then the link number represents the link number of the link the message originated from. If the head link is transmitting a message, then the link number represents the link that is targeted by the message. A link number of 0 signifies a broadcast with no intended target, such as the Sync Data message.

The messages are divided into 4 types based on their OpCode:

- `0x01 -> 0x04`: Emergency messages
- `0x05 -> 0x06`: Sync Messages
- `0x08 -> ____`: Control messages 
- ____ -> ____: Sensor messages
- ____ -> ____: Config messages


## OpCodes


| Command | Name             | Use                                                                                                                            |
| ------- | ---------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| `0x00`  | PANIC            | Immediately stop all motors. Do not accept any further control messages until reboot                                           |
| `0x01`  | FATAL ERROR      | An error has occurred that prevents normal function                                                                            |
| `0x02`  | WARNING          | An event has occurred that should not normally be happening                                                                    |
| `0x03`  |                  |                                                                                                                                |
| `0x04`  |                  |                                                                                                                                |
| `0x05`  | SYNC SENSOR      | A notifier message to a link (or all links) to send its configured sensor measurements onto the CAN bus                        |
| `0x06`  | SYNC CONTROL     | Causes all links to enact the latest control message received from the head link.                                              |
| `0x07`  | SET JOINT ANGLE  | Sets the reference angle for position control. Ignored if the control mode of the link is not set to ANGLE                     |
| `0x08`  | SET JOINT ANGVEL | Sets the reference angular velocity for angular velocity control. Ignored if the control mode of the link is not set to ANGVEL |
| `0x09`  | SET JOINT TORQUE | Sets the reference torque for torque control. Ignored if the control mode of the link is not set to TORQUE                     |