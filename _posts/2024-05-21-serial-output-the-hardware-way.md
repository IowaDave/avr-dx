---
description: simple serial output using hardware directly
title: "Serial Output the Hard(ware) Way"
---

# Serial Output the Hard(ware) Way

This article will explain how to:

* **transmit text from an unmounted AVR Dx microcontroller** to a computer using the Dx USART hardware module directly.
<br><br>
"Unmounted" means the controller is wired directly into a circuit, rather than indirectly as a component incorporated into an Arduino development board product. 
<br><br> "Using ... hardware ... directly" means avoiding the ```Serial``` class methods.

* **route the transmissions to a computer's USB port** through older-generation Arduino in a go-between role. 

I will do my best to keep this short.

<blockquote>
Now let me say right here, upfront, that programs designed to run on Dx-family devices having large-enough memory capacity, such as the 64K of flash in my AVR64DD28, might as well avail themselves of all the preprogrammed Serial I/O and print formatting methods available in the <a href="https://github.com/SpenceKonde/DxCore">Dx Core library</a> for Arduino IDE generously provided on Github by Spence Konde and friends. The full boatload adds about 4K into the compiled code of a project, leaving nearly 60K for the user's program. But that's for future applications.
</blockquote>

My goal in this series is to learn how the hardware works. It is a general theme of my life, about everything: study how it works; go beyond merely learning "how to work it". 

What follows aims for better understanding how the USART hardware inside a Dx controller transmits data.

### Problem
I'm using Arduino IDE to compile code for an unmounted AVR Dx microcontroller. The IDE uploads code to the Dx through an Arduino Nano (Classic) clone development board running a firmware that turns the Nano into a UPDI programmer. 

I would like to send text strings from the Dx device back to the computer. The Dx program will need to:

1. generate the strings;

2. transmit them out through the Dx controller's hardware; and

3. conduct the strings into the computer.

Steps #1 and #2 will avoid using the ```Serial``` class methods of the IDE in the example program, as a way to focus on the hardware.

Step #3 will be addressed by passing the Dx transmissions through a "go-between" Arduino Nano board for re-transmission via the Nano's USB interface.


### Solution to #1 - ```sprintf()```

Declare a buffer as an array of 8-bit bytes. Make it large enough to contain the longest string your code might generate. This requires some thought beforehand. Thinking is hard, I know. But that's the only hard part about this step. 

The buffer can be any size that fits in the available SRAM memory. Suppose I determine that my longest string would not exceed 60 characters. I can allow room for it, plus an end-of-line byte (0x00), plus an extra byte or two for elbow room, this way:

```char outputBuffer[64];```

Build strings in the buffer using the C/C++ ```sprintf()``` statement. Arduino IDE also makes the ```dtostr()``` statement available for converting floating-point numbers to strings.

### Solution to #3 - a Passive, Intermediary Arduino

Signals emerging from a microcontroller need to pass through a "go-between" device on their way to a computer. FTDI devices cost money and require specialized driver software. A spare Arduino Uno or Nano (classic) can also serve, as follows (See Figure 1, below):

* Upload a blank program onto the Arduino board. 

* Connect the Tx pin of the unmounted Dx-family controller to the Tx pin of the Arduino.  For example, the Tx pin of a Nano is digital 1, often labeled "Tx1". The Tx pin of my AVR64DD28 is pin PA0, at external pin position 22 on the chip's package. Repeating to be clear, it goes Tx-to-Tx.

* Optionally, connect the two Rx pins. I don't use them in this article.

* Be sure to connect the Gnd pin of the Dx to the Arduino's Gnd pin. You can power the Dx chip from the +5V pin of the Arduino board or from some other, 5-volt source. Keep it all at 5 volts.

* Plug the Arduino into the USB port of the computer.

![AVR64DD28 Tx Connected to Nano Clone]({{site.baseurl}}/images/Nano-go-between.png)
<br>**Figure 1. Nano Clone Connected to AVR64DD28 Tx-to-Tx**

This setup works because the Arduino Uno or Classic Nano has on its board a device that takes the signals appearing on the Tx and Rx pins of the board and transfers them out via USB. The Arduino does this regardless of whether the signals are put onto those pins by a program running on the Arduino or (as in this case) originating somewhere else instead.

### Solution to #2 - USART Direct

The acronym stands for **U**niversal **S**ynchronous and **A**synchronous **R**eceiver and **T**ransmitter. It tells you that the thing not only receives and transmits but also knows more than one way to do it:

* asynchronous full duplex with no clock (like Arduino Serial)
* half duplex
    * One-Wire
    * RS-485
* Synchronous (adds a clock line)
    * Host
    * Client
    * SPI Host
* Infrared

This article ignores almost everything the USART can do. It focuses on transmitting, only. To do so with a minimal amount of compiled code, a program can write directly to the hardware, which will in turn do most of the work.

The datasheet lists four steps when setting up the USART for this purpose. They are identified and described briefly in Table 1, below, then labeled and detailed separately in the example program that follows it.

| Step | Setup |
| ---- | ----- |
| &#49;&#46; Set the baud rate (USARTn.BAUD). | Calculate a constant based on the desired BAUD rate and the system clock speed. The data sheet gives the formula for the calculation. Store the result into USART's BAUD register. |
| &#50;&#46; Set the frame format and mode of operation (USARTn.CTRLC).| For example: asynchronous mode with 8 character bits plus 1 stop bit but no parity bit. |
| &#51;&#46; Configure the TXD pin as an output. | Determine which pin the USART will transmit through, and put that pin into OUTPUT mode. |
| &#52;&#46; Enable the transmitter (USARTn.CTRLB). | Write a certain bit to logic "1" in the USART's CTRLB register.  |

**Table 1 - Set Up the USART for Asynchronous Transmission**

The example program below borrows a macro and two function definitions from an [example published on Github by Microchip, Inc](https://github.com/microchip-pic-avr-examples/avr128da48-usart-example).

* The macro defines an inline function, ```USART_BAUD_RATE(BAUD_RATE)```, implementing the formula given in the datasheet for calculating the value to write into the BAUD rate hardware register.

* ```USART0_sendChar(char c)``` transmits the byte ```c``` by writing it to the USART's TXDATA register. From there, the hardware will transmit it automatically.


* ```USART0_sendString(char *str)``` transmits the characters in the buffer pointed to by ```str```. All it needs to do is to call ```USART0_sendChar(char c)``` repeatedly, once for each character in the buffer.

```
/*
 *   Serial output - asynchronous transmission
 *   Writing directly to the USART hardware
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
 * Pin PA0 is the default Transmit (Tx) pin.
 * (Optional) Pin PA6 blinks an LED
 */

/*
 * The following macro and two function prototpyes
 * are adapted from an example published 
 * by Microchip, Inc. on Github at the following URL, accessed in May 2024
 * https://github.com/microchip-pic-avr-examples/avr128da48-usart-example
 */
#define USART_BAUD_RATE(BAUD_RATE) \
  (uint16_t) ((float)(64 * F_CPU \
  / (16 * (float)BAUD_RATE)) + 0.5)           // inline macro to set baud rate
void USART0_sendChar(char c);                 // prototype, sends a char
void USART0_sendString(char *str);            // prototype, sends a string

/* program variables */
uint16_t x = 0;                               // tracks message number
char outbuf[64];                              // buffer for the output string

void setup() {

  /* enable output pins */
  PORTA.DIRSET =
      PIN0_bm                                 // USART Tx output
    | PIN6_bm;                                // Optional LED

  /* configure USART0 */
  USART0.BAUD = USART_BAUD_RATE(115200);      // set BAUD rate 115200
  USART0.CTRLC = 
    USART_CHSIZE_8BIT_gc;                     // 8-bit char, no parity, 1 stop bit  
  PORTA.DIRSET = PIN0_bm;                     // OUTPUT mode for transmit (Tx) pin 
  USART0.CTRLB = 
      USART_TXEN_bm;                          // enable the transmitter

  USART0_sendString("\r\n");                  // transmit a blank line

} /* end of setup process */

void loop() {

  delay(1000);
  x += 1;                                     // increment the message counter
  sprintf(outbuf,                             // specify the outbuf buffer and 
    "Message # %u: Hello World!\r\n", x);     // build the message string in it

  USART0_sendString(outbuf);                  // transmit the buffer
  USART0_sendString("\r\n");                  // transmit a blank line

  PORTA.OUTTGL = PIN6_bm;                     // toggle an LED on PA6

} /* end of loop process */


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
        ;             // wait for the Tx data register to become available
    }
    
    USART0.TXDATAL = c;                       // write c into the register
}

void USART0_sendString(char *str)
{
    for(size_t i = 0; i < strlen(str); i++)    
    {        
        USART0_sendChar(str[i]);    
    }
}

```

### Remarks on the example program

The macro, ```USART_BAUD_RATE(BAUD_RATE)``` makes use of a second macro, ```F_CPU```, which is defined at compile-time to a value equal to the system clock frequency. It is used throughout the Arduino Core library wherever a process needs to take account of how fast the system clock is running. 

The Arduino Core for Dx modifies the Arduino IDE Tools menu to offer a choice of system clock speeds prior to compiling the code. Making a selection in the menu determines both the setting of the actual clock speed and the corresponding value of ```F_CPU```.

Everything about this code is "blocking", meaning that it ties up the CPU. There are better approaches for tracking time and operating the USART that would free up the CPU to do other work.

I would normally use a timer interrupt instead of ```delay()```. Likewise, I would want to make use of USART interrupts. For that purpose based on my know-how at the time I wrote this in May 2024, I would choose to use the Serial class methods provided in the Arduino Core libraries, as those are interrupt-driven.

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
