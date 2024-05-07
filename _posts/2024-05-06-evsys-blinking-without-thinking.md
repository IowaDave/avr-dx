---
description: introduces the Event System of a Dx controller
title: "Events - Blinking Without Thinking"
---

# Events &mdash; Blinking Without Thinking

The Event System, called EVSYS, routes signals from one place to another inside an AVR Dx controller, such as my AVR64DD28.

### Definitions

What does that mean? A:

* "**signal**" is just a voltage level, or a change in the level. Note that it specifies a *voltage*, saying nothing about any *current*.
* "**route**" is a physical pathway, like a jumper wire but traced on the silicon, by which a signal arising in one place can be sensed in another, different place.
* "**place**" refers to the peripherals built into the microcontroller, such as an I/O pin, a timer, or the analog-to-decimal converter (ADC).

It means one can establish a circuit inside the microcontroller whereby a signal originating at one place can control an action at another.

### How Does It Differ from an Interrupt?

If you think about it, interrupts carry a signal from a peripheral to the CPU. The CPU jumps right away to a block of code called an interrupt service routine (ISR). The code in an ISR certainly can control other parts of the device.

In that way, an interrupt is a particular kind of event.

The difference is that events can target other peripherals rather than the CPU. They route signals directly from one place to another, bypassing the CPU. The target peripheral can be configured to take a definite action upon receiving the event. 

Some kinds of signals, called "asymmetric events", can trigger the targeted actions actually faster than the CPU can execute even a single code instruction.

Through events, peripherals can communicate amongst themselves and initiate coordinated tasks without waiting for the CPU to tell them what to do.

All of that, and one more thing. Events may require less code to set up, compared to interrupts and ISRs.

### Seeing Is Believing

It is now hands-on-the-breadboard time. The following steps enable a switch *with almost no current passing through it* to drive current in another, different circuit, turning a light on and off. 

**Step 1**: establish the switch as the signal's source. Connect:

* resistor "R0", 10K Ohms, between pin PD7 and ground to pull the pin low,
* a switch between pin PD7 and an open row on the breadboard,
* resistor "R1", 5K Ohms, connecting that row to the same voltage that powers the microprocessor, called Vcc. 

The switch gives the ability to apply a voltage signal onto pin PD7 without passing much current.

When the switch is open, resistor R0 drains the voltage on pin PD7 down to ground, signaling a "LOW Level". Closing the switch pulls up the voltage on PD7 to Vcc through resistor R1, signaling a "HIGH Level".

**Step 2**: enable pin PA2 to energize the light. Connect:
* the anode of an LED to pin PA2, and
* the cathode of that LED through a suitable resistor, such as 330 Ohms, to ground.

**Step 3**:
Somehow, the signal received on pin PD7 must actuate a current on pin PA2.

A microcontroller lacking an Event System would have to run a program designed to monitor the voltage level on the input pin, PD7, and energize or de-energize the output pin, PA2 accordingly. It could be done either by polling the input continuously or by means of an interrupt. Either way, the CPU plays an active role in actuating the output pin, thus operating the light.

However, the Event System bypasses the CPU so that, in effect, the input pin *directly* controls the output pin. A program needs only three instructions to set things up.

```
// configure PD7 as INPUT
  PORTD.OUTCLR = PIN7_bm;            // clearing bit 7 makes PD7 an INPUT

  EVSYS.CHANNEL2 =                   // tell channel 2 to exhibit the voltage
    EVSYS_CHANNEL2_PORTD_PIN7_gc;    // being presented on pin PD7

  EVSYS.USEREVSYSEVOUTA =            // set the output level of a certain pin, named "EVOUTA",
    EVSYS_USER_CHANNEL2_gc;          // to match the signal present on Channel 2
```

### What Does That Code Do?

Putting pin PD7 into INPUT mode configures it to sense a voltage without accepting any current. 

Table **3.1 I/O Multiplexing** on page 14 of the datasheet identifies EVOUTA as one of the alternative roles that pin PA2 can perform. Activating the Event channel and routing its signal to EVOUTA automatically puts PA2 into OUTPUT mode and places the Event System channel in control of it, superseding the pin's default role of general purpose input-output. 

The CPU plays no further role after executing the three instructions. However, and this is important, the *microcontroller* remains involved. When the hardware senses HIGH voltage (with no current) on pin PD7, *the hardware* automatically energizes and sources current out through pin PA2.

In other words, at the risk of becoming tedious, this program *gives the same result* as one in which code monitors the input pin and activates the output pin &mdash; but without the code.

### Oh. How. Sweet. Another Blinkin' LED... Big Deal, Right?

Yes, *right*, actually big. Consider that a microcontroller able to turn on an LED can also actuate a circuit that turns on all the lights in a room, or lights up a building, or starts a motor, or brings a whole power plant online, or you-name-it, at the mere touch of a low-voltage switch, while using so little current that even its CPU is not running.

Parents urge children to think before they act. Yet in microcontrollers, a talent for action without a need to crunch some code can be a good thing.
 
### How To Blink Without Thinking

This article concludes with a slightly longer program that does the same thing as the listing above, but takes a periodic signal generated internally by the RTC peripheral rather than one from an input pin.

```
/*
 * Blink an LED through the Event System
 *   Event generator: RTC PIT div 512 takeoff
 *   Event user:      Pin PA2
 *   
 * Designed for AVR Dx microcontrollers and tested with AVR64DD28.
 * 
 * Copyright (c) 2024 David G. Sparks  All rights reserved.
 *
 * This program is free software; you can redistribute it and/or
 * modify it under the terms of the GNU Lesser General Public
 * License as published by the Free Software Foundation; either
 * version 2.1 of the License, or (at your option) any later version.
 *
 * This program is distributed in the hope that it will be useful,
 * but WITHOUT ANY WARRANTY; without even the implied warranty of
 * MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
 * Lesser General Public License for more details.
 *
 * To obtain a copy of the GNU Lesser General Public
 * License, write to the Free Software Foundation, Inc., 
 * 51 Franklin St, Fifth Floor, Boston, MA  02110-1301  USA
 */

/*
 * Connect an LED and resistor to pin PA2
 */

void setup() {

/*  
 *   Prepare the RTC 
 */
//   Wait for the RTC to synchronize with the system clock
  while (RTC.STATUS & RTC_CTRLABUSY_bm) { ; /* wait */ }
  //   Select the OSC1K clock source giving 1024 clocks per second
  RTC.CLKSEL = RTC_CLKSEL_OSC1K_gc;

  //   Wait for the PIT to synchronize with the system clock
  while (RTC.PITSTATUS & RTC_CTRLBUSY_bm) { ; /* wait */ }
  /*   
   * Enable PIT and 
   * Set the PIT period = 1024 / 2 = CYC512 = 1/2 second.
   * Note that PIT will toggle every 1/2 Period,
   * giving a full on-then-off cycle each 1/2 second.
   */
  RTC.PITCTRLA = 
       RTC_PERIOD_CYC512_gc
     | RTC_PITEN_bm;  
     
  EVSYS.CHANNEL1 =                    // tell Channel 1 to exhibit the signals coming
    EVSYS_CHANNEL1_RTC_PIT_DIV512_gc; // the PIT_DIV512 takeoff of the RTC module's prescaler

  EVSYS.USEREVSYSEVOUTA =             // set the output level on pin PA2
    EVSYS_USER_CHANNEL1_gc;           // according to the signal present on Channel 1
}

int main ()
{
  setup();    // start the PIT and route its signal to pin PA7
  return 0;   // end the program, leave only the hardware running
}
```

### Almost Too Easy To Believe

Something there is in my mind that craves complexity and cannot immediately accept simple explanations.

I spent several hours re-reading Section 16 in the datasheet, thinking, "There must be more to it. Surely I fail to see the subtle trick hidden here."

Finally I did what seems to work best when all else fails: open the device header file and find in there all of the definitions that correspond to the register names and the parameter values named in the data sheet for this module.

Eventually a pattern emerges and one sees what to copy from the header file and paste into the program. It becomes obvious, after one figures out what to look for.

It would have been nice to find a worked example in the datasheet. I hope the examples in this article may help someone else to get a grip on the Event System a little faster than I did.

### Related Articles

Links to the datasheet documentation are provided in the "Getting Started" and "Where to Learn More" articles listed below.

The Device header file is explored in detail in "Understanding the Device Header File".

Real Time Counter and its Periodic Interrupt Timer (PIT) are described in the "RTC Real Time Counter" article.

<hr />

### Other Posts in this Blog 

<ul>
  {% for post in site.posts %}
    <li>
      <h4>
        <a href="{{site.baseurl}}{{ post.url }}"       
        {% if post.title == page.title %}
           style="color: black;"
        {% endif %}>{{ post.title }}
        </a>
        
        {% if post.title == page.title %}
          &nbsp; << You are here.
        {% endif %}
        
      </h4>
    </li>
  {% endfor %}
</ul>