# Link on-board Electronics

![Electronics overview](static/electronics.png)

This section specifically covers the electronics onboard each link in the Boa snake robot. 


## Microcontroller Board
The electronics are centered around the _Microcontroller Board_ which in our case is an Adafruit Feather M4 CAN. The _Microcontroller Board_ coordinates the control of the servo, transmissions over the CAN bus and reception and processing of sensor signals. The M4 CAN was chosen for this robot for 2 main reasons:

1. The board features a powerful ATSAME51J19 microcontroller using an ARM Cortex-M4 core running at 120MHz. The blazing clock speed allows the M4 CAN, combined with the hardware floating point support of the Cortex-M4 architecture, allows the M4 CAN to do some serious processing. The M4 CAN would have sufficient processing power to run real-time filtering algorithms or control loops.
2. The board has built-in support for CAN bus, including on-board voltage transceivers and ready-made firmware libraries 

All electronics connected to the _Link Board_, except for the servo is powered by 3.3V delivered by the M4 CAN which contains a 5V to 3.3V regulator. This means that the control electronics of a Boa snake robot link can be powered either from the _LV power bus_ or from the USB-C connector of the M4 CAN. 

The M4 CAN communicates with the other subsystems in the link using UART, I2C and SPI protocols.

The _Microcontroller Board_ is seated on the _Link Board_.


## Link Board
The Link Board is a custom built PCB which encompasses a couple of features

- The _Link Board_ features connectors that attach to the LV power bus, IMU board, K3D40 Force Sensor and Servo 
- The amplifier circuitry for the K3D40 Force Sensor is located on the _Link Board_.
- The _Link Board_ contains an RS485 voltage level transceiver, which allows the _Microcontroller board_ to use a hardware UART to communicate with the servo.  

The _Link Board_ has a single M3 mounting hole used to secure the board to the _PCB Holder_


## Servo communication
The RMD-X6 servo in the Boa snake robot comes with two options as to which protocol it uses for communications. The "standard" version comes with a built-in CAN connection, implying that each servo could (theoretically) be connected straight onto the spine bus of the Boa. While tantalizing in theory, this solution has some practical issues which will be discussed soon.

We opted for the the alternative version of the RMD-X6 which uses a standard UART for communications, using the RS485 protocol and voltage levels. In order to communicate with the servo at RS485 voltage levels, the _Link Board_ contains an RS485 transciever which is connected directly to one of the hardware serial ports of the _Microcontroller Board_ on the TLL side. The servo can handle baudrates up to 2MHz making the transmission of serial data nearly instant wrt. latency in a control context.

So why not use CAN directly? Well, there are several reasons:

1. The application level protocol used by the servos to communicate over CAN is not particularly extensible, poorly documented and written in spectacular dialect of chinese-english. It would be quite difficult to adapt the communications from both the _Brain_ and the _Link Board_ to comply with the communications from the servo.
2. The protocol used by the servos only support up to 8 devices on the same bus (This number varies depending on where in the documentation you look).
3. Bypassing the _Link Board_ and connecting the Servo straight onto the CAN-bus messes with the concept of a hierarchical structure in the communications systems of the Boa. Look at TODO: Link to communications


## The IMU board
The IMU board is a separate little PCB, connected to the _Link Board_ through a Adafruit Stemma QT cable. The IMU board is basically a breakout board for the Bosch BNO085 IMU chip, which is an amazing piece of technology in its own right. The BNO085 contains all the guts of a "regular" IMU, but it also contains its own ARM Cortex-M0+ processor which is running a suite of proprietary sensor fusion algorithms to produce better sensor values. The full array of different measurements the IMU can procure is listed in TODO: Add link to docs 

Only a subset of theses measurements are used in the first iteration on the Boa snake robot, but some of these measurements might be interesting for future research, such as tap-detection (perhaps it can be used for collision detection?)

The connection between the _Link Board_ and the IMU Board is run at the maximum baudrate of the BNO085 which is 400KHz.

The BNO085 was chosen for this project due to its impressive array of high-quality measurements and its ease-of-use through the breakout board. Having the IMU on its own PCB is also an advantage, as it allows us to place the IMU anywhere on the link, and not necessarily where the _Link Board_ is placed. 


## The Constriaint Force Sensor (CFS) 
The CFS system is a complex system which I could write an entire chapter about. I'll do my best to keep this section short and to-the-point. The CFS system was one of the main reasons why we decided to build the Mamba and also a huge part of the component costs of building the robot. The task of finding a suitable sensor for measuring the constraint forces in a joint was outsourced to a wonderful team of B.Sc. students [Melhuus et.al. 2020]. Their thesis is worth a read to understand how difficult it was to find suitable force sensors within an acceptable price range.

We landed on using a strain gauge based multi-axis force sensor named K3D40 from the German company ME-Messysteme. While quite costly, the sensor is small enough to fit inside the snake robot, while still being tough enough to handle a bit of rough handling. The K3D40 is a "barebones" force sensor and includes no amplifiers or control electronics on its own. The K3D40 requires an external exitation voltage for its strain gauges, as well as amplifier electronics and ADC conversion. ME-Messysteme provides amplifiers for the sensor, but unfortunatly these are all intended for industrial or desktop use, an are waaaay to big to fit inside a rather small snake robot. We decided to build our own tiny amplifier circuit, tailored to our own needs. The design is inspired by the work of [Melhuus et.al. 2020]. 

The cable coming out of the K3D40 is terminated in a JST SH-series 12-lead connector, which is used to connect the sensor to the _Link Board_. The amplifier and ADC circuitry is placed directly on the _Link Board_.

The K3D40 containts 3 strain gauge bridges, which measure one axis of force each. In a planar snake robot, we are primarily interested in only 2 of the axes, but the sensor is capable of measuring all 3. Maybe the vertical force component could be used for something interesting in the future? The _Link Board_ supplies a 3.3V excitation voltage to each strain gauge. The supply voltage is filtered using some capacitors to try and reduce any noise from other components on the _Link Board_. The sensor returns a tiny differential voltage in the mV range, with a common mode voltage of half the supply voltage (1.65V) for each strain gauge bridge. 

Each differential voltage is amplified using a INA826 Instrumentation Amplifier to produce a slightly stronger (and thus more noise resistant) differential signal. The INA826 was chosen due to its decent slew rate, low noise and high common mode rejection. 

The amplified differential signal is then passed on to an ADS131M04 ADC chip. The ADC converts the differential voltage into a digital value, which is relayed to the _Microcontroller Board_ using SPI.

The ADS131M04 is a relatively new chip which uses the Sigma-Delta approach (which is absolute sorcery) to sample analog voltages at a high rate and with an astounding resolution of 24 bits. The ADS131M04 also samples up to 4 channels simultaneously, which is ideal for sampling our 3 differential signals. The fourth channel remains unused.


## A note on firmware
The firmware running on the _Microcontroller Board_ is contained in ![its own repository](https://github.com/Boa-Snake-Robot/Boa-Link-Firmware). The code should be fairly well commented and should be quite legible. The code shows all implementation details wrt. communication between the _Microcontroller Board_, the ADS131M04, the IMU board, the servo and the spine CAN bus. 