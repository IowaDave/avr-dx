# Blink And Ye Shall Learn

![AVR64DD28 blinking an LED]({{site.baseurl}}/images/AVR64DD28_LED.jpg)

Blinking an LED has been called the "Hello World" program for microcontrollers.

This article will compare a classic "Arduino language" demonstration program, *Blink*, to an example designed to reveal differences in the new Dx family of microcontrollers, compared to the older ATmega328P found on Arduino Uno and Nano boards.

I adapted the classic demo to activate pin PA6 (PORT A, Pin 6) on the new chip, to show that it will certainly compile and run on a Dx chip such as my AVR64DD28. 

Arduino IDE considers pin PA6 to be the digital pin number 6. The 28-pin DIP version of the chip seen in the photo, above, brings pin PA6 out at position 28, the upper-left corner in this view. 

The classic Arduino *Blink* sketch listed below was running as the photo was taken, and the targeted LED behaved just as one would expect it to do with every other Arduino product that runs this same code.

<pre><code>
     1	/*
     2	  Blink
     3	
     4	  Turns an LED on for one second, then off for one second, repeatedly.
     5	
     6	  modified 8 May 2014
     7	  by Scott Fitzgerald
     8	  modified 2 Sep 2016
     9	  by Arturo Guadalupi
    10	  modified 8 Sep 2016
    11	  by Colby Newman
    12	  adapted for unmounted AVR64DD28
    13	  by David G. Sparks 28 Apr 2024
    14	
    15	  This example code is in the public domain.
    16	
    17	  https://www.arduino.cc/en/Tutorial/BuiltInExamples/Blink
    18	*/
    19	
    20	// the setup function runs once when you press reset or power the device
    21	void setup() {
    22	  // initialize digital pin 6 as an output.
    23	  pinMode(6, OUTPUT);
    24	}
    25	
    26	// the loop function runs over and over again forever
    27	void loop() {
    28	  digitalWrite(6, HIGH);   // turn the LED on (HIGH is the voltage level)
    29	  delay(1000);                       // wait for a second
    30	  digitalWrite(6, LOW);    // turn the LED off by making the voltage LOW
    31	  delay(1000);                       // wait for a second
    32	}
</code></pre>

My new example that follows is deliberately written in "bare metal" code to illuminate differences that the Arduino libraries handle internally, beyond a casual user's view. 

<blockquote>
People are free to discuss the advantages and disadvantages of bare-metal coding compared to reliance on Arduino libraries. Just &mdash; <em>please</em> &mdash; no loud, profane or profound talking, and step outside before the fur begins to fly! &lt;grin&gt;
</blockquote>

### Dx chips differ from the ATmega328P in many ways, including:

* managing the system clock speed,
* writing to write-protected registers,
* new types of timer/counters,
* "helper" registers for managing I/O pins
* how interrupts are handled, and
* the way peripheral registers are named.

### The Dx Code

I'll put the code here first, then talk about it.

<pre><code>
     1	/*
     2	 * Blink an LED on an AVR64DD28
     3	 * Using timer TCB0 periodic interrupt.
     4	 * Avoid using any Arduino setup code.
     5	 * 
     6	 * Copyright (c) 2024 David G. Sparks  All right reserved.
     7	 *
     8	 * This program is free software; you can redistribute it and/or
     9	 * modify it under the terms of the GNU Lesser General Public
    10	 * License as published by the Free Software Foundation; either
    11	 * version 2.1 of the License, or (at your option) any later version.
    12	 *
    13	 * This program is distributed in the hope that it will be useful,
    14	 * but WITHOUT ANY WARRANTY; without even the implied warranty of
    15	 * MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
    16	 * Lesser General Public License for more details.
    17	 *
    18	 * To obtain a copy of the GNU Lesser General Public
    19	 * License, write to the Free Software Foundation, Inc., 
    20	 * 51 Franklin St, Fifth Floor, Boston, MA  02110-1301  USA
    21	 */
    22	
    23	#define BLINK_COUNT 500 // about 1/2 second at the rate this thing will go
    24	
    25	void setup() {
    26	
    27	  /* 
    28	   *  Select a system clock speed of 8 MHz, for sentimental reasons.
    29	   *  Writing this setting must follow a timed sequence
    30	   *  that should not be interrupted.
    31	   */
    32	  cli();                                            // disable interrupts globally
    33	  CCP = CCP_IOREG_gc;                               // initiate the sequence
    34	  CLKCTRL.OSCHFCTRLA = CLKCTRL_FRQSEL_8M_gc;        // write the frequency selection value
    35	  sei();                                            // re-enable interrupts globally
    36	
    37	  /*
    38	   * Configure Timer/Counter0 to generate interrupts
    39	   * to control regularly recurring operations.
    40	   * 
    41	   * Take input from the system clock.
    42	   * Operate in the Periodic Interrupt (INT) counting mode.
    43	   * Set the Capture register to a value of 8,000
    44	   * for an interrupt at 1/1,000th second intervals.
    45	   * 
    46	   * An Interrupt Service Routine toggles an LED
    47	   * after a programmable number of interrupts. 
    48	   */
    49	
    50	  TCB0.CTRLA = 0;                                   // select system clock and disable the timer
    51	  TCB0.CTRLB = TCB_CNTMODE_INT_gc;                  // select mode INT and zero the other bits
    52	  TCB0.EVCTRL = 0;                                  // no event generation
    53	  // The next steps should not be interrupted
    54	  cli();                                            // disable interrupts globally
    55	  TCB0.CCMP = 8000;                                 // 16-bit compare value for 250 interrupts per second
    56	  TCB0.INTFLAGS = TCB_CAPT_bm;                      // clear capture (match) interrupt
    57	  TCB0.INTCTRL = TCB_CAPT_bm;                       // enable capture (match) interrupt
    58	  // OK to re-enable ingterrupts now
    59	  sei();                                            // enable interrupts globally
    60	  TCB0.CTRLA |= TCB_ENABLE_bm;                      // enable the timer
    61	/*
    62	 * Enable pin PA6 for OUTPUT
    63	 * and set its initial drive level HIGH
    64	 */
    65	  PORTA.DIRSET = PIN6_bm;
    66	  PORTA.OUTSET = PIN6_bm;
    67	
    68	}
    69	
    70	void loop() {
    71	  // nothing happens here
    72	}
    73	
    74	/*
    75	 * Supply own main() code block
    76	 * to prevent IDE from loading its default
    77	 * configuration code.
    78	 */
    79	 
    80	int main()
    81	{
    82	  setup();                                        // call the setup() block once
    83	  while (1) {loop();}                             // stay in the loop() forever
    84	  return 0;                                       // always end main() this way
    85	}
    86	
    87	ISR(TCB0_INT_vect)
    88	{
    89	  static unsigned int interruptCount = 0;           // local persistent storage container
    90	  cli();                                            // 16-bit arithmetic ahead; halt interrupts
    91	  TCB0.INTFLAGS = TCB_CAPT_bm;                      // clear capture (match) interrupt
    92	  if (interruptCount++ >= BLINK_COUNT)              // check number of interrupts, with post-increment
    93	  {
    94	    interruptCount = 0;                             // reset the counter when a match occurs
    95	    PORTA.OUTTGL = PIN6_bm;                         // toggle the pin when a match occurs
    96	  }
    97	  sei();                                            // resume interrupts
    98	}
</code></pre>

### What the Code Does

A timer/counter is configured to generate interrupts 1,000 times per second. An interrupt service routine (ISR) counts the interrupts.

Line 23 defines a constant, 500. 

The ISR compares the count to the constant. It toggles the LED after counting 500 interrupts, about one-half second of elapsed time. Then it re-starts the count from zero.

The ```loop()``` code block is empty, as the program runs entirely in the ```setup()``` block and in the ISR.

A ```main()``` block is defined. We do not normally do this in Arduino programs. Instead, since C++ *requires* a ```main()``` block, Arduino IDE inserts one of its own by default. When user code includes a ```main()``` block, however, the compiler uses it rather than the Arduino default one.

This decision cuts several hundred bytes out of the compiled program size. It leaves out features such as ```millis()```, which this program does not need. It also frees up resources, including timers, that those features otherwise consume.

### System Clock Speed

The Dx family claims to provide (among other clock sources) a "High-precision internal high-frequency oscillator with selectable frequency up to 24 MHz (OSCHF)."

It sounds great and is the default choice. Let us use it.

Dx controllers give us two ways to regulate the system clock speed when using the OSCHF source. One way is to pre-scale it (divide by a power of 2) down to a lower frequency, which is also how the 328P does it. This program takes the other path instead. It accepts the Dx chip's default setting to *not* pre-scale the source.

The other way is to change the speed of the OSCHF itself. Frequencies ranging from 1 MHz up to 24 MHz can be selected. The hardware's default setting is 4 MHz. This program changes it to 8 MHz, for no good reason beyond a nostalgic whim to match the 328P's internal oscillator speed.

(There is even a way to boost this chip's speed all the way up to 48 MHz: a future article.)

Line 34 changes the oscillator speed by writing a predefined constant value into an OSCHF Control Register. The value, 0x05, is given in the **Frequency Select** table on page 106 of the data sheet. The device header table defines self-descriptive names all of the choices in that table:

<pre><code>
/* Frequency select */
typedef enum CLKCTRL_FRQSEL_enum
{
    CLKCTRL_FRQSEL_1M_gc = (0x00<<2),  /* 1 MHz system clock */
    CLKCTRL_FRQSEL_2M_gc = (0x01<<2),  /* 2 MHz system clock */
    CLKCTRL_FRQSEL_3M_gc = (0x02<<2),  /* 3 MHz system clock */
    CLKCTRL_FRQSEL_4M_gc = (0x03<<2),  /* 4 MHz system clock (default) */
    CLKCTRL_FRQSEL_8M_gc = (0x05<<2),  /* 8 MHz system clock */
    CLKCTRL_FRQSEL_12M_gc = (0x06<<2),  /* 12 MHz system clock */
    CLKCTRL_FRQSEL_16M_gc = (0x07<<2),  /* 16 MHz system clock */
    CLKCTRL_FRQSEL_20M_gc = (0x08<<2),  /* 20 MHz system clock */
    CLKCTRL_FRQSEL_24M_gc = (0x09<<2)  /* 24 MHz system clock */
} CLKCTRL_FRQSEL_t;
</code></pre>

### Writing to Write-protected Registers

But wait. The OSCHF control register is write-protected. The program must notify the hardware to allow the write. A timed sequence is required which must not be interrupted.

Line 32 disables interrupts globally.

Line 33 writes a predefined 8-bit key value into the Configuration Change Protection register, CCP. The hardware will then accept a change to the OSCHF control register within the next four clock cycles. Line 34 gets it done.

Line 35 re-enables interrupts globally.

ATmega328P chips do something similar but differently. Their procedure involves writing a specific bit in the target register.

### New Types of Timer/Counters
This program takes advantage of a Timer/Counter Type B, which the ATmega328P does not contain. The AVR64DD28 device contains three of these timers, TCB0, TCB1 and TCB2. Additionally it has two other types of timers, not discussed here.

Lines 50 through 60 configure TCB0 to generate periodic "capture" interrupts at a rate of 1,000 times per second. Refer to Section 24.3.2 on pages 276-277 of the data sheet.

Note that the example program does not use the ```delay()``` feature of Arduino Language. Instead, elapsed time is metered in an interrupt service routine (ISR), discussed below.

### Helper Registers for I/O Pins

The Dx device PORT peripherals bear a family resemblance to those in the ATmega328P controllers. In both, a PORT peripheral will contain registers named DIR and OUT.

DIR regulates the pin mode and OUT governs the voltage present on a pin more or less the same way in both products. Setting or clearing bits in those registers will alter the state of the pins connected to them.

However, the Dx devices provide additional, "helper" registers giving code writers other, more code-efficient ways to manage the PORT pins.

* DIRSET, DIRCLR and DIRTGL respectively set, clear and toggle bits in the DIR register.
* Likewise, OUTSET, OUTCLR and OUTTGL switch the voltage bits in the OUT register.

Lines 65, 66 and 95 use some of the new helper registers.

In fact the I/O pins on a Dx must serve many more peripherals compared to those on the ATmega328P. To manage such versatile demands, Microchip added more registers to the Dx PORT group. For example, each individual pin has a Pin Control register. 

An added PORTMUX module referees all the different roles a pin might be asked to play. It plays no role here and belongs in a future post.

### Interrupts Are Handled Very Differently

ATmega328P controllers do some very nice things automatically when an interrupt occurs, including: 

* disable interrupts when entering an Interrupt Service Routine (ISR);
* clear the interrupt flag in the affected peripheral when entering the ISR; and
* re-enable global interrupts when exiting the ISR.

Alas, that was then and this is now, and these new DX chips *do not* do those nice things. 

It becomes the code writer's responsibility to enable or disable interrupts and to clear peripheral interrupt flags.

Lines 90, 91 and 97 take care of the business. 

There is one more difference regarding interrupts. Peripherals which can flag two or more, different reasons to interrupt may be constrained to activate only one, shared ISR. The TCB0 is an example. 

It is capable of flagging both a "capture" and an "overflow" interrupt. Yet, as line 87 indicates, TCB0 is given only a single ISR. It is a limitation burned by lasers onto the very silicon. 

For such peripherals, the ISR must determine which interrupt flag to act upon. This program avoids the problem by enabling only the "capture" interrupt, in line 57 of the setup() procedure. The ISR would have to poll the available flags if more than one interrupt were enabled in a given peripheral.

### A New Way of Naming Registers

Hardware registers had unique, one-word names in the ATmega328P. That was possible because there was only one instance of each, different peripheral.

These new, Dx family devices needed a new plan because they can contain more than one, identical instance of some peripherals. As described in my post on the Device Header File, the peripherals become C++ ```struct```-type variables, inside of which the different registers are members. The "dot" operator is use to join the struct and member names for access to the contents of the registers.

Lines 34, 50-52, 55-57, 60, 65-66, 92 and 95 demonstrate the new way of names.

### Conclusion

The first rule of new tools is to learn to use them. 

Dx-family microcontrollers differ in many ways compared to older device families in the AVR lineup. Microchip added features to them that the older models lack, while retaining certain qualities I still admire in the older ATmega328Ps. 

Old men need to take their fun sitting down, and I enjoy learning the new Dx chips. 

If Microchip were to discontinue the ATmega328P, or price it too high, I would comfortably select a Dx model. The AVR64DD28 looks good enough to me. I am happy that they produce a PDIP28 version of this modern device.

I shall keep supplies of both kinds on hand, subject to availability, because either an AVR64DD28 or an ATmega328P would perform well for most of my purposes.

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



