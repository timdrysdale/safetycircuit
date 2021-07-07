# safetycircuit
Design for a physical circuit to protect hardware that is being controlled by learner programmers

## Doc status

2021 July 7 - Rough draft of the motivation/requirements (subject to major change)

## Motivation
We want to create open-ended remote laboratory experiences for students capable of undertaking embedded systems programming, with a greater or lesser amount of help from the experimental interface.

Open-ended remote laboratory experiences are traditioanlly a little more difficult to arrange due to the degree of pre-preparation requried to connect an experiment to the remote browser of a student, and due to safety requirements. 

However, embedded systems programmers are expected to gain confidence and deeper understanding of issues relating to interrupt service routines, real time operating systems, and so forth, if they are given bare-metal access to the microcontroller they are working on. In a related way, senior students may wish to create custom programming for remote laboratory experiments they are working on, potentially with some help in the construction of the embedded code.

Yet, the safety of the experiment is often ensured through good design of the firmware that we now propose to let students create. 

To do this safely, we must ensure the safety of the equipment even if the student firmware fails to operate correctly.


## Background

Remote labs offer efficient ways to let learner programmers develop and test embedded software on real hardware, such as sensors and motors. As any embedded systems developer will know, one of the risks to manage is the system entering a damaging state and becoming unresponsive. Some typical cases include:

- continuing to power a motor when the driven apparatus has already reached its physical limit
- setting outputs that cause a short circuit, e.g. enabling both sides of an H-bridge at once

If you are physically present, you can simply remove the power from the system and/or take further action as required. 

If you are not physically present, then with an unresponsive system, there may be very little you can do at all to prevent system damage. This risk needs to be mitigated in order to make remote programming of bare-metal embedded systems a practical, safe, enjoyable, and economic proposition.

### Mitigation methods

#### Software

Static code analysis could potentially be deployed to detect code producing undesirable outputs. It is potentially difficult to obtain a formal proof of correctness for systems with multiple interacting state machines, unreachable states, states that are reached through the action of external noise or interference applied to the system and so on. It might even require a full formal model of the underlying hardware to be included, to account for cases where code is directly writing to registers etc. To me, that seems a long way round, for a partially satisfactory answer. Can we do simpler, and better?

If you are interested in the concept of proving programmatic correctness, then start [here](https://ocw.mit.edu/courses/electrical-engineering-and-computer-science/6-042j-mathematics-for-computer-science-spring-2015/readings/MIT6_042JS15_Session9.pdf) for an intro.

#### Hardware

A physical circuit, interposed between the microcontroller and the hardare, can take responsibility for ensuring only safe ouputs are applied to the system. It must of course be adequately informed of the permissible states of the attached hardware. It should be able to prevent unsafe states from occurring across a broad range of relatively straightforward experiments. It will be possible to find exceptions to this - after all, we know that we don't yet trust driverless cars on all types of roads, and we still have humans up front in airplanes, trains, and boats, despite impressive degrees of automation. Remote lab experiments which cannot be protected adequately can presumably be identified through the process of attempting to build a circuit which can protect them - and failing to do so. The rest of this doc fleshes out the concept of this circuit.

## Requirements

 - rules hardwired at programming time to ensure protection always in place
 - support invariant parameters
 - support conditional parameters
 - support reconfigurable digital pins (in, or out)
 - support analogue inputs on pins which can also be digital in/out
 - communicate over USB using JSON to send and receive status information
 - support external switching of power supplies (digital output)
 - support SPI/I2C temperature monitoring (digital protocol)
 - support quadrature encoder input
 - support external "emergency off" input (e.g. if there is external 
 - support time-based rules (min/max on-time, min/max off-time, min/max PWM duty cycle, min/max PWM frequency)
 - place no additional limitations on how the student microcontroller is configured (this can be relaxed to a limited degree for practical reasons)
 - able to process all inputs / outputs at all times, no risk of blanking or missing signals
 - retain original signal timing to within the timing error implicit in the student microcontroller chip design 
 
 
### likely design consequence -> FPGA not microcontroller

In order to meet the timing, parallelism, and miss-no-signals goals, as well as being able to have custom rules, a Field Programmable Gate Array is the optimum platform. A microcontroller would introduce additional delays if interrupts-on-change are not serviced promptly, especially if multiple pins change simultaneously, and may even miss signals due to being occupied on other tasks such as communicating with peripherals (encoders, temperature sensors, serial comms to experiment monitoring software)


 
 
 
 
 
 
 


