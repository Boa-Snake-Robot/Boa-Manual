# Spine Protocol Specification

## Introduction

The arbitration ID is separated into 2 parts 

Bits 0 through 5 represent the Link number the message is intended for with a value from 0 to 64. 

Bits 6 through 10 of the arbitration ID represent the OpCode, with a value from 0 to 31

Because of the way CAN works, the OpCode will also determine the priority of the messages. A message with a low OpCode will take arbitration over a message with a high OpCode. This is why the safety critical messages such as error reporting and controll messages have low OpCodes, and low priority messages such as configuration messages have high OpCodes.  

If a link is transmitting the message, then the link number represents the link number of the link the message originated from. If the head link is transmitting a message, then the link number represents the link that is targeted by the message. A link number of 0 signifies a broadcast with no intended target, such as the Sync Data message.

The messages are divided into 4 types based on their OpCode:

- `0x01 -> 0x04`: Emergency messages
- `0x05 -> 0x06`: Sync Messages
- `0x07 -> 0x09`: Control messages 
- `0x0B -> 0x12`: Sensor messages
- `0x14 -> 0x17`: Config messages


## OpCodes

| OpCode | Name                | Use                                                                                                                            |
| ------ | ------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| `0x00` | PANIC               | Immediately stop all motors. Do not accept any further control messages until reboot                                           |
| `0x01` | FATAL ERROR         | An error has occurred that prevents normal function                                                                            |
| `0x02` | WARNING             | An event has occurred that should not normally be happening                                                                    |
| `0x03` |                     |                                                                                                                                |
| `0x04` |                     |                                                                                                                                |
| `0x05` | SYNC SENSOR         | A notifier message to a link (or all links) to send its configured sensor measurements onto the CAN bus                        |
| `0x06` | SYNC CONTROL        | Causes all links to enact the latest control message received from the head link.                                              |
| `0x07` | SET JOINT ANGLE     | Sets the reference angle for position control. Ignored if the control mode of the link is not set to ANGLE                     |
| `0x08` | SET JOINT ANGVEL    | Sets the reference angular velocity for angular velocity control. Ignored if the control mode of the link is not set to ANGVEL |
| `0x09` | SET JOINT TORQUE    | Sets the reference torque for torque control. Ignored if the control mode of the link is not set to TORQUE                     |
| `0x0A` |                     |                                                                                                                                |
| `0x0B` | SENSOR LINK ACC     | IMU Measurement of link acceleration in local frame                                                                            |
| `0x0C` | SENSOR LINK VEL     | IMU Measurement of link velocity in local frame                                                                                |
| `0x0D` | SENSOR LINK ANGVEL  | IMU Measurement of link angular velocity                                                                                       |
| `0x0E` | SENSOR LINK ANGLE   | IMU Measurement of link angle                                                                                                  |
| `0x0F` | SENSOR JOINT ANGVEL | Measurement of joint angular velocity                                                                                          |
| `0x10` | SENSOR JOINT TORQUE | Measurement of joint torque                                                                                                    |
| `0x11` | SENSOR JOINT ANGLE  | Measurement of joint angle                                                                                                     |
| `0x12` | SENSOR JOINT CFS    | Measurement of joint constraint force                                                                                          |
| `0x13` |                     |                                                                                                                                |
| `0x14` | CONTROL MODE        | Set the control mode of the servos to Position, Speed or Torque                                                                |
| `0x15` |                     |                                                                                                                                |
| `0x16` | ENABLE SENSOR       | Enables a sensor measurement that a link should broadcast on a Sync Sensor message                                             |
| `0x17` | DISABLE SENSOR      | Disables a sensor measurement that a link should broadcast on a Sync Sensor message                                            |

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