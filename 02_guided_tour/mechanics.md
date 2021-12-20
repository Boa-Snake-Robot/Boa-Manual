![alt text](static/doge.jpeg)

# Chassis 

The chassis of the Boa snake robot is the main structural part of the robot. All transfer of torque and force between links is done through the chassis, and it is also the main contact point between the robot and its surrounding environment. Several previous snake robots were modeled around the idea of a more traditional or frame with an external "shell" or "skin" attached on the outside. Much like the Mamba snake robot, Boa is designed around a design principle called *monocoque* (don't laugh) where the outer shell of the robot is also the main frame. Formula 1 race cars are designed around the same principle, and do not have any internal frame apart from the carbon-fiber body.

The chassis is milled from a very specific type of polymer plastic called Acetal Copolymer (POM-C or just POM). POM is often used in moving parts of high-speed machinery as bushings or bearings. It's also commonly used in the food and chemical industry, due to its very high resistance to corrosion and deterioration. POM has a couple of material properties making it very suitable for the Boa snake robot:

- It is extremely rigid. POM-C has very low elasticity and does not deform easily. It also has very little creep and deteriorates very little over time. This is also essential for the dynamics of the system, as a rigid system is much easier to model properly
- It has a very low friction coefficient. This, combined with the "toughness" of the material, means that the chassis can interact with the environment surrounding the Boa without being damaged while minimally changing the dynamics of the robot 
- POM can be milled with standard M3 threads making it easy to attach parts to the chassis
- I comes in many different colors, which is fun. We chose black because it's cool (That's literally the only reason)

One drawback of using POM is that it is quite dense. The chassis of the Boa snake robot is probably unnecessarily heavy, and can probably take loads and forces way higher than what the Boa can generate using its servos. The chassis could probably be made thinner and lighter without affecting the performance of the robot. We chose to not spend a lot of time making the chassis more "optimized" as the weight of the chassis isn't all that important when working in a plane and we don't have to fight gravity. The servos are quite strong and should be able to pull the weight without further issue

The chassis is peppered with M3 holes on the side walls and bottom which are not used. This is quite intentional, as it makes it really easy for future researchers to attach new parts or gadgets to the outside of the chassis. Make sure to blow the holes with some canned air before inserting screws to remove any dust or debris. 


# Non-structural parts
The non-structural parts are all mechanical parts of the Boa snake robot which carry little or no load. They are usually for cosmetic purposes, or to contain sensors or electronics. All of these parts are 3D-printed in PETG plastic (The same used in coke bottles!) on a Prusa MK3S printer. PETG was chosen for its durability and because it's really easy to print. The competitors were ASA/ABS plastic or PLA. ASA/ABS is somewhat stronger but is _horrible_ to print with (It warps, bends and generally misbehaves, and ABS makes some nasty stinky fumes when printing). PLA is a decent alternative, but PLA is biodegradable and deteriorates over time. PETG is generally stronger than PLA. Also, PLA is more brittle than PETG making it more likely to crack when screws are inserted. All parts were made using Prusament brand filament. This is important as low-quality filament might make the parts weaker and the printing less precise

All the parts are colored a bright neon orange color. The bright color makes it easier to take decent pictures of the robot, compares to black or gray, and it also looks great with the black of the chassis :) The non-structural parts are: 

- **Bulkhead:** The primary non-structural part of the Boa snake robot, and the only one that is directly attached to the chassis. The Bulkhead is the mounting point for the PCB-holder and the Top Hat, and also serves as protection against dust and debris for the more sensitive parts of the Boa snake robot. The Bulkhead has two "pockets" to contain Wago lever nuts to attach the HV Power Bus to the link. The Bulkhead also contains compartments where wires and cables can be bunched together to make cable management easier

- **PCB-holder:** Holds the PCB. Duh. 

- **Top Hat:** Covers the top of each link. Serves as dust and debris protection for the onboard electronics in the links, as well as being the attachment point for each Cable Bridge

# Motor coupling

The motor coupling consists of a couple of parts which connect the links together. These are vital components as they must be relatively compact to fit inside the joint between each link while being strong enough to endure all torque and forces being transmitted through a joint. The motor coupling consists of 2 pieces of milled aluminium named something as creative as _Upper Motor Coupling_ and _Lower Motor Coupling_. These two discs serve as the connection between _Constraint Force Sensor_ and the servo motor

# Passive joint

The passive joint is a vital, although hidden, part of the Boa snake robot. The passive joint is a specially designed compliant mechanism that connects the chassis of a link to its servo. the passive joint is designed to introduce some flexibility in the robot in all dimensions which are not interesting to examine in a planar snake robot, while remaining stiff in the dimensions that we want to examine. If the robot was to operate on a surface that is slightly uneven or not completely flat, the passive joint will make sure that no strange forces or torques are introduced to the robot which might affect performance. It will also ensure that each link lies "flat" on the ground, improving friction properties and making friction forces more predictable. An additional function of the passive joint is that it is the weakest part among the structural parts of the Boa snake robot. Under extreme loads, the passive joint will break first, preventing damage to the rest of the robot. In a sense, it works kind of like a fuse. This is nice feature as the passive joint is the cheapest structural part to replace.

The design of the passive joint is inspired by a mechanical part called a _beam coupling_ or _helical coupling_

The passive joint is completely rotationally stiff on the z-axis (along the motor axis), and is translationally stiff along the x-axis and y-axis (parallel to the surface). The joint is translationally compliant along the z-axis and rotationally compliant on the x-axis and y-axis.

This is achieved using three parts (but really just 2):

- **Spring Disc:** The spring disc is the part in the passive joint that is actually compliant. The thickness and material in the spring disc can be adjusted to reduce or increase the compliance in the passive joint. The Boa robot was originally designed with a spring disc 3D-printed from PETG plastic.

- **Upper Spring Seat:** A spacer between the servo and the _Spring Disc_ 

- **Lower Spring Seat:** A spacer between the _Spring Disc_ and the chassis. This part does not exist as it is built into the chassis