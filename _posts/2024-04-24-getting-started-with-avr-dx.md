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

The headers are also available to read in the DxCore web repository. The day I wrote this note, the AVR64DD28 header was available at [https://github.com/SpenceKonde/DxCore/blob/master/megaavr/extras/ioheaders/ioavr64dd28.h](https://github.com/SpenceKonde/DxCore/blob/master/megaavr/extras/ioheaders/ioavr64dd28.h).

The official data sheet and the silicon errata complete a minimal set of essential documentation. They can be accessed from the documentation area of the product page on the manufacturer's web site:

[https://www.microchip.com/en-us/product/avr64dd28](https://www.microchip.com/en-us/product/avr64dd28)

### Remarks

#### Gratitude

I feel gratitude for the freely donated resources allowing hobbyists like me to work with Microchhip's new Dx series of AVR microcontrollers using the familiar Arduino IDE.

The next cowpoke to ride in here may prefer a different development environment, which is fine. Good examples I have worked with include Microchip's Atmel Studio and MPLAB solutions, along with their proprietary ATMEL ICE programmer hardware.

Every tool needs time to learn to use it. Arduino IDE enjoys my favor mostly because it gets the job done and I feel most familiar with it.

That is why I really appreciate those github repositories keeping Arduino IDE up to date with the new AVR hardware. 

Moreover, my limited experience as a contributor to the community makes me applaud the enormous amount of know-how and work those few dozen volunteers have so generously given. 

Available is better than perfect, yet they continue to improve the resource. Let us cheer them on as they do.

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
