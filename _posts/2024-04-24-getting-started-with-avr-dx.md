---
description: About AVR Dx Coding
---

# Getting Started with AVR Dx
Begin exploring the AVR64DD28 microcontroller

The day will surely come to say goodbye to our trusty, old, familiar ATmega328P chips we learned to love on Arduino Uno development boards.

My preference in a replacement includes availability of a 28-pin DIP package. Microchip offers a number of such products in its modern, "Dx" lineup. I chose the AVR64DD28 for further study.

Its features compare very favorably to those of the old 328s. See the data sheet, listed below. 

### What I Use to Program the Device
I write and compile code for it in the Arduino IDE, the older version 1.8.15. A dedicated Arduino Nano serves as a UPDI programmer for uploads into a bare AVR64DD28 controller.

The following links gave useful information at the time of access in April 2024:

[https://github.com/ElTangas/jtag2updi](https://github.com/ElTangas/jtag2updi) for the "programmer" code loaded onto the Nano, turning it into a UPDI programmer. The site has a schematic for wiring the AVR64DD28 to the Nano and describes using avrdude to contact it. 

A useful version of the avrdude utility was obtained by adding the Arduino Nano Every board to my Arduino IDE with the Boards Manager.

The AVR Dx boards were added to the IDE also, from [https://github.com/SpenceKonde/DxCore/tree/master](https://github.com/SpenceKonde/DxCore/tree/master). 

Those "DxCore" resources make it possible to write programs in "Arduino language" for the Dx chips after installing the board files in the IDE. 

DxCore is a large, impressive, and ongoing body of work contributed by several dozen truly generous volunteers. I have more to say about this in the [remarks](#remarks) section, below.

Alternatively, code can control the device by reading from and writing to the internal hardware registers directly. This is called "bare-metal" coding. 

Advantages for bare-metal coding include access to more powerful features of the controller that pre-built "Arduino" functions do not implement. 

Arduino IDE places the Dx board files in the "Arduino15" folder on the local computer. For each, different model, there is a unique header file that readers should know how to find. For example on my Mac, the relevant header for the AVR64DD28 is found at 

<blockquote>

/Users/david/Library/Arduino15/packages/DxCore/tools/avr-gcc/7.3.0-atmel3.6.1-azduino7/avr/include/avr/ioavr64dd28.h

</blockquote>

(By the way, there is *not* a typo in the file path. It really does say "azduino". But it compiles correctly, which means we little ponies must wear that imperfect saddle quietly.)

It is not necessary for user code to #include the device header file in a program. The IDE takes care of it automatically after selecting the device in the IDE's Tools menu.

What makes the header file so useful is that it spells out the actual names to use when writing bare metal code. I keep it open in a text editor for frequent reference. The header implements the naming rules for registers and the data stored in them, as given in the product data sheet.

By the way, the headers are also available to read in the DxCore web repository. The day I wrote this note, the AVR64DD28 header was available at [https://github.com/SpenceKonde/DxCore/blob/master/megaavr/extras/ioheaders/ioavr64dd28.h](https://github.com/SpenceKonde/DxCore/blob/master/megaavr/extras/ioheaders/ioavr64dd28.h).

The official data sheet and the silicon errata complete a minimal set of essential documentation. They can be accessed from the documentation area of the product page on the manufacturer's web site:

[https://www.microchip.com/en-us/product/avr64dd28](https://www.microchip.com/en-us/product/avr64dd28)

### Remarks

#### Gratitude

I feel gratitude for the freely donated resources allowing hobbyists like me to work with Microchhip's new Dx series of AVR microcontrollers using the familiar Arduino IDE.

The next cowpoke to ride in here may prefer a different development environment, which is fine. Good examples I have worked with include Microchip's Atmel Studio and MPLAB solutions, along with their proprietary ATMEL ICE programmer hardware.

Every tool needs time to learn to use it. Arduino IDE enjoys my favor mostly because it gets the job done and I feel most familiar with it.

That is why I really appreciate those github repositories keeping Arduino IDE up to date with the new AVR hardware. Moreover, my limited experience as a contributor to the community makes me appreciate the enormous amount of know-how and work those few dozen volunteers have so generously given.

#### Where to Learn More about the New AVRs

Start with the documentation on the DxCore repository site at Github. Here, for example, is a clear-eyed and candid assessment --- good, bad and ugly --- of the AVR64DD28:

[https://github.com/SpenceKonde/DxCore/blob/master/megaavr/extras/DD28.md](https://github.com/SpenceKonde/DxCore/blob/master/megaavr/extras/DD28.md)

The manufacturer also provides extensive discussion of its design intentions for the new Dx series by comparing them to an earlier lineup called *megaAVR*. The following link was active when I wrote this note in April, 2024. Kindly take note that Microchip is notorious for moving files around on its web site. 

[Migration from the megaAVR to AVR Dx Microcontroller Families](https://onlinedocs.microchip.com/pr/GUID-51D4F2DF-E4D3-4379-8E03-9AAF2593C7DA-en-US-3/index.html?GUID-824D7D20-C233-4D55-BBC8-00AE9BCEB048]

Going back into history for an example, the Arduino Nano Every board is based on the megaAVR4809 controller. A web search will bring up plenty of discussion comparing the Nano Every to the older Arduino Nano board, based on the even older, ATmega328P processor.


For background and overview of all the AVRs, see also another of Spence Konde's repostories on Github, [https://github.com/SpenceKonde/AVR-Guidance/tree/master](https://github.com/SpenceKonde/AVR-Guidance/tree/master).

Like all products made by humans, these Dx chips might not do everything their manufacturer says it wants them to do. It's worthwhile to read the Silicon Errata document. Pay attention to any clarifying language in the data sheet also.

Finally, Microchip publishes a very large number of feature-focused "Technical Briefs". Sometimes I find them very helpful. I leave the finding of those documents as homework for the intrepid reader.

#### Why Choose AVR 8-bit Controllers?

In a word, simplicity. But first, some words in favor of the alternatives.

Let us recognize that competing, 32-bit ARM-based devices have surely won the "arms race" by sheer megatonnage of processor speed and memory capacity. A 32-bit address bus can access gigabytes, while the 16-bit bus of an 8-bit AVR must perform gymnastics to reach beyond 64K.

Given greater capabilities, ARM-based microcontrollers can evolve towards becoming single-chip computers. Heck, some of them probably already qualify as such.

Increased memory and speed allow more peripherals onto the silicon die. It's only natural that they become interconnected in complex ways. 

A code designer can give such a controller more tasks to manage contemporaneously. Writing the code becomes more complex, as the different peripherals and processes in play compete for access to the CPU. Multi-core CPUs exist in the ARM line-up to address that problem.

In that wonderful world, a code writer with good sense will resort to some kind of operating system (OS) for two reasons that I can see: multitasking and an Application Programming Interface (API).

We can obtain a degree of multitasking on the simpler, 8-bit AVRs, so I won't discuss that further here.

Now to discuss my preference for simpler devices.

The concept of an API is where my preference, as a hobbyist program writer, bends away from the ARMs toward the 8-bit AVRs.

An API stands between the user's code and the hardware. User code tells the API what it wants the hardware to do.

An API is nothing more than a Big Block 'O Code that runs behind the scenes. In that sense, even the Arduino core library for a device is an API. 

The people who write an API decide which parts of the microcontroller to make available and how they will operate. Consider PWM for example. An API will determine that PWM for a certain pin will be implemented on one, definitely chosen timer, will have one, predetermined frequency, with a preselected set of different duty cycles to choose.

That's nice. Just tell the API to activate PWM on that pin with a chosen duty cycle and presto! there it is.

However, you might need to use that timer a different way, or want the PWM on a different pin, at a different frequency, with perhaps a finer degree of control over the duty cycle.

Really complex systems grow to insist on "the API way" as the only way to go. It's understandable. However, is it necessary?

Most microcontroller applications involve repeatedly waiting for something to happen then performing some action in response. 

A simple example might be a tea-timer that waits for a button to be pressed then counts down some interval of time before turning off. I made one that has been running for months on a set of AA-size batteries.

A more complex example is the circuit that turns on a light when a passive infrared sensor detects a person present in a room and turns if off a short time after the sensor no longer detects the presence.

Applications like those don't need megabytes of memory, let alone the overhead of an operating system and an API.

The 8-bit AVRs give code writers straightforward, direct access to their hardware. A single instruction changing one bit in one, 8-bit memory location suffices to turn an LED on or off.

Even an uneducated amateur like me can learn enough from the data sheet and the device header file to write an instruction like that.

Plus, learning how to tell the hardware, myself, in my own way, what I want it to do is what I call Fun. Which makes me weird, I admit. Deal with it.

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
