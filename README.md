# The Boa Manual

Welcome to the manual pages for the Boa snake robot!

If you're looking at this page at GitHub, a more reading-friendly version can be found at [this page](https://boa-snake-robot.github.io/Boa-Manual/).

This manual should serve as your one-stop-shop for anything Boa-related.  If you're new to the Boa robot 
(i.e. you are a new M.Sc. or PhD. student), i strongly suggest that you read the entire manual front to back. It may be more information than you need,
but it will give you some insight into how the Boa works. 

**A caveat before continuing:**
I personally find technical documents spectacularly boring. Why can't science be fun? While this manual contains a lot of boring (but reaaally important) technical stuff,
i've done my best to make it interesting, fun and motivating to read. Because of this, do not expect this manual to have a level of seriousness or
language fitting for a scientific paper. It contains plenty of personal opinions, small stories, digressions and musings. 


The manual is divided into 3 chapters:

## [1 - Introduction](01_introduction/index.md)
This chapter says something about the background for building the robot, the design process and the design principles for the robot. This chapter might
answer such questions as 
- Why was the Boa snake robot built?
- When was the Boa snake robot built?
- What is the design of the Boa snake robot inspired by?

This chapter also contains a dictionary of all terms used when talking about snake robots in general and specifics of the Boa snake robot


## [2 - A Guided Tour](02_guided_tour/index.md)
This chapter contains all of the nitty-gritty technical details of the Boa snake robot. This chapter goes into depth on each subsystem of the 
Boa snake robot and explains the purpose of the major components in the system. This chapter also contains details of any data protocols used
in the development of the robot. This chapter is primarily intended for those of you that want to repair, upgrade or modify the robot. This
chapter answers questions such as

- What is the baudrate used in the I2C bus between the IMU and the Link Board?
- What material is used in the chassis of the Boa snake robot?
- How does the Boa measure constraint forces in the joints?
- Why is the Boa orange?

_The answer is 400 kBaud, Acetal Copolymer, strain gauges, and because it looks really cool, if you were curious_ 

**Another small caveat about this section:**
While most of the subsystems and major components are documented in this section, you are required to look at schematics, source code and
CAD models to get the full overview of the internal workings of the Boa. Everything should be stored in the [https://github.com/Boa-Snake-Robot](Boa Snake robot organization) on GitHub. The repository is (as of 2022) non-public, and you will need to be added to the organization by an administrator to see all the relevant files

## [3 - Getting Started](03_getting_started/index.md)
This chapter simply explains how to use the Boa snake robot. This chapter will hold you by the hand and take you through every step of using the Boa, from 
from booting it for the first time, to communicating with the robot and using the software API. This chapter answers questions such as

- How do i turned the damned thing on?
- What does the big scary red button on the base station do?
- Why is there a flashing red light on one of the links, and why wont the Boa move when this happens?
