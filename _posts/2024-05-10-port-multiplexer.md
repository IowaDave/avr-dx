---
description: examines the PORT pin multiplexer of AVR Dx
title: "Shape Shifter - The Port Pin Multiplexer"
---

# Shape Shifter
### The PORT Pin Multiplexer

I/O pins can perform more than one, different role. General input/output as directed by program code is their default. Peripherals that can receive or transmit through certain, designated external pins take over those pins automatically when enabled. For example, the USART0 module will use pins 0 and 1 on PORT A. The datasheet names them PA0 and PA1.

Default pins work fine until your program needs those pins for some other, different purpose. In that case, a program can use the Port Multiplexer module to designate an alternative pin assignment.

When might the Port Multiplexer come into play?

### Serial I/O, For Example

I chose the USART0 module to illustrate this article, because it is the hardware used by default for the  ```Serial```  communications object in a conventional Arduino program.

Exhibit 1 reproduces the pinout diagram from the datasheet for my selected device, the SPDIP28 package of an AVR64DD8 controller. Pins PA0 and PA1 are located halfway along the right-hand side in this view, at pin locations 22 and 23, respectively.

![SPDIP28 Pinout Diagram]({{site.baseurl}}/images/SPDIP28_Pinout.png)
<br>**Exhibit 1. SPDIP28 Pinout Diagram**

Look again. Something else, cryptically named XTALHF, short for *e***XT***ern***AL** **H***igh* **F***requency crystal oscillator*, also appears for those two pins *and nowhere else*.
The different uses for a pin are mutually exclusive, meaning one-at-a-time. Pins PA0 and PA1 cannot perform Serial I/O after your application attaches an external high-speed crystal to the device.

We need to assign Serial I/O to an alternative pair of pins. How do we know which ones?

**Table 3.1 I/O Multiplexing** on pages 17-18 in the datasheet displays the different roles different pins can take on. An excerpt appears in Exhibit 2, below. It highlights some of the pin choices for the USART0 module, the hardware used by the  ```Serial```  communications object in a conventional Arduino program.

![excerpt of Table 3.1]({{site.baseurl}}/images/USART0_Multplx.png)
<br>**Exhibit 2. Portion of Datasheet Table 3.1 I/O Multiplexing**

The default pins for USART0 appear within the tall, vertical ovals, along the top two rows of Exhibit&nbsp;2. 

The smaller, dashed oval appearing on those same two rows highlights that XTALHF is a competing use for the same pins. 

Horizontal ovals a few rows below indicate that pins PA4 and PA5 located at positions 26 and 27 provide an alternate I/O pair for USART0.

A program can direct I/O for USART0 through PA4 and PA5, instead of PA0 and PA1, by writing to a register in the Port Multiplexer module. 

### Where To Make the Change

The PORTMUX (PORT pin MUltipleXer) module provides a central place to assign pins for the eight peripheral modules that allow choosing among several I/O pins. Each such peripheral has its own register in PORTMUX. They are listed on page 156 of the datasheet, and at the end of this article, under [Peripherals Having I/O MUX](#peripherals-having-io-mux).

The USART example in this article has the "USART Route A" register. Its working name is the mashed-up concatenation, USARTROUTEA.

Exhibit 3 is an excerpt from the data sheet detailing the USARTROUTEA register. 

![USARTROUTEA Register]({{site.baseurl}}/images/USARTROUTEA.png)
<br>**Exhibit 3. USARTROUTEA register in the PORTMUX module**

The USART0 peripheral determines which pins to use by the 3-bit value written into the bitfield named USART0, in the register named USARTROUTEA of the PORTMUX module.

The default value in this field will be 0x0 following power-on reset, indicating the USART0 module should use pins PA0 and PA1.

Writing the value 0x1 there would cause the USART0 module to transmit and receive through pins PA4 and PA5. The alternate pin assignment should be performed prior to enabling the affected peripheral.

Arduino IDE gives programmers two ways to do it.

Pay attention to how that value, "1", shows up in the following example program.

### Example Program using ```Serial``` statements

The ```Serial``` commands rank among the so-called "Big Three" groups of statements in the Arduino Core libraries. This first example uses them. Notice that ```Serial.swap()``` comes *before* ```Serial.begin()```.

```
/*
 *   Serial output on an alternate, non-default I/O pin
 *   Using the SERIAL object defined in Arduino Core libraries
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
 * Pin PA4 will be the Transmit (Tx) pin.
 * Pin PA5 will be the Receive (Rx) pin.
 */

uint16_t x = 0;                     // Tracks the message number.

void setup() {
  Serial.swap(1);                   // Route Serial I/O to PA4 and PA5
  Serial.begin(115200);             // Initialize USART0 at 115,200 BAUD           
  while (!Serial) { ; }             // Allow init to complete
  Serial.println();                 // Tell the World we're in business
}

void loop() {
  x += 1;                           // Increment the message number
  Serial.print("Message #");        // Start the message
  Serial.print(x);                  // print the message numbrr
  Serial.println(": Hello World!"); // finish saying Hello to the World
  Serial.println();                 // Skip a line
  delay(1000);                      // Wait a second
}
```

```Serial.swap(1)``` is the "Arduino Language" way of setting conditions such that ```Serial.begin()``` will institute the alternate pins PA4 and PA5. 

```Serial``` is an elaborate class spanning multiple header files and many hundreds of lines of code.  Somewhere in all of that, the ```.swap()``` member function interacts with the Port Multiplexer. I won't go into it further here in this article about the PORTMUX module.

Instead, I give you a second example that avoids using "Arduino Language", writing instead directly to the hardware registers that make it all go.  This program produces the identical output as that above.

### Example Program Writing Directly to PORTMUX 

The first statement in the setup() process listed below reads,

```
  PORTMUX.USARTROUTEA                           // Port Multiplexer USART register
    |= PORTMUX_USART0_ALT1_gc;                  // select pin group 1 for USART0
```

Here the program writes the alternate selection of pin group 0x1 directly to the right place in the USART pin routing register in the Port Multiplexer.

```
/*
 *   Serial output on an alternate, non-default I/O pin
 *   Using the SERIAL object defined in Arduino Core libraries
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
 * Pin PA4 will be the Transmit (Tx) pin.
 * Pin PA5 will be the Receive (Rx) pin.
 */

/*
 * The following macro and two function prototpyes
 * are adapted from an example published 
 * by Microchip, Inc. on Github at the following URL, accessed in May 2024
 * https://github.com/microchip-pic-avr-examples/avr128da48-usart-example
 */

#define USART_BAUD_RATE(BAUD_RATE) \
  ((float)(64 * F_CPU \
  / (16 * (float)BAUD_RATE)) + 0.5)             // inline macro to set baud rate

void USART0_sendChar(char c);                   // prototype, sends a char
void USART0_sendString(char *str);              // prototype, sends a string


uint16_t x = 0;                                 // tracks message number
char outbuf[32];                                // buffer for the output string

void setup() {
  PORTMUX.USARTROUTEA                           // Port Multiplexer USART register
    |= PORTMUX_USART0_ALT1_gc;                  // select pin group 1 for USART0
           
  USART0.BAUD = USART_BAUD_RATE(115200);        // set BAUD rate 115200
  USART0.CTRLC = 
    USART_CHSIZE_8BIT_gc;                       // 8-bit char, no parity, 1 stop bit  
  PORTA.DIRSET = PIN4_bm;                       // OUTPUT mode for transmit Tx) pin 
  USART0.CTRLB = 
      USART_TXEN_bm;                            // enable the transmitter

  USART0_sendString("\r\n");                    // mimics Serial.println();

}

void loop() {

  x += 1;                                       // increment the message number
  sprintf(outbuf,                               // specify the outbuf buffer and 
  "Message # %u: Hello World!\r\n", x);         // build the message string in it

  USART0_sendString(outbuf);                    // mimics Serial.println();
  USART0_sendString("\r\n");                    
  
  delay(1000);
}


/*
 * The following functions are adapted
 * from an example published by 
 * Microchip, Inc. on Github
 * at the following URL, accessed May 2024
 * https://github.com/microchip-pic-avr-examples/avr128da48-usart-example
 */

void USART0_sendChar(char c)
{
    while(!(USART0.STATUS & USART_DREIF_bm))
    {
        ;
    }
    
    USART0.TXDATAL = c;
}

void USART0_sendString(char *str)
{
    for(size_t i = 0; i < strlen(str); i++)    
    {        
        USART0_sendChar(str[i]);    
    }
}
```

Again, details regarding the USART module are outside the scope of this article.

This "going-direct" example lacks a method for waiting until the USART module is ready for work. I have not found a way to do that &mdash; yet.

<h3 id="peripherals-having-io-mux">Peripherals Having I/O MUX</h3>

The PORTMUX module controls I/O pin routing in separate registers for each of the following peripherals in my AVR64DD28. Note that PORTMUX may have give more choices for other packages having a greater number of I/O pins.

| Peripheral | PORTMUX Register | Remarks |
| ---------- | ---------------- | ------- |
| Event System | EVSYSROUTEA | Output pins on Ports A, D and F |
| CCL | CCLROUTEA | Outputs for tables 2, 1 and 0 |
| USART | USARTROUTEA | Configure I/O for both USART0 and USART 1 |
| SPI | SPIROUTEA | Choose one of five different I/O pin groups for SPI |
| TWI (I2C, IIC) | TWIROUTEA | I/O pin groupings for Host, Client and Dual modes|
| TCA | TCAROUTEA | Direct Timer/Counter A waveforms to one of four, different Ports|
| TCB | TCBROUTEA | Alternate output pins for TCB0 and TCB1 but not for TCB2 |
| TCD | TCDROUTEA | Three different sets of output pins for the four waveforms it generates |

Pages 155 - 164 in the datasheet cover the PORTMUX module.


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