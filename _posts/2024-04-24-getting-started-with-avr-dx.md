---
description: About AVR Dx Coding
---

# Getting Started with AVR Dx
Begin exploring the AVR64DD28 microcontroller

The day will surely come to say goodbye to our trusty, old, familiar ATmega328P chips we learned to love on Arduino Uno development boards.

My preference in a replacement includes availability of a 28-pin DIP package. Microchip offers a number of such products in its modern, "Dx" lineup. I chose the AVR64DD28 for further study.

Its features compare very favorably to those of the old 328s. See the data sheet, listed below. 

### How I Program the Device
I write and compile code for it in the Arduino IDE, the older version 1.8.15. A dedicated Arduino Nano serves as a UPDI programmer for uploads into a bare AVR64DD28 controller.

The following links gave useful information at the time of access in April 2024:

[https://github.com/ElTangas/jtag2updi](https://github.com/ElTangas/jtag2updi) for the "programmer" code loaded onto the Nano, turning it into a UPDI programmer. The site has a schematic for wiring the AVR64DD28 to the Nano and describes using avrdude to contact it. 

A useful version of the avrdude utility was obtained by adding the Arduino Nano Every board to my Arduino IDE with the Boards Manager.

The AVR Dx boards were added to the IDE also, from [https://github.com/SpenceKonde/DxCore/tree/master](https://github.com/SpenceKonde/DxCore/tree/master). 

Programs can be written in "Arduino language" for the Dx chips after installing the board files in the IDE.

Alternatively, code can control the device by reading from and writing to the internal hardware registers directly. This is called "bare-metal" coding. 

Advantages for bare-metal coding include access to more powerful features of the controller that pre-built "Arduino" functions do not implement. 

Arduino IDE places the Dx board files in the "Arduino15" folder on the local computer. For each, different model, there is a unique header file that readers should know how to find. For example on my Mac, the relevant header for the AVR64DD28 is found at 

```
/Users/david/Library/Arduino15/packages/DxCore/tools/avr-gcc/7.3.0-atmel3.6.1-azduino7/avr/include/avr/ioavr64dd28.h
```

(By the way, there is not a typo in the file path. It really does say "azduino". But it compiles correctly, so we little ponies must wear that saddle quietly.)

It is not necessary for user code to #include the device header file in a program. The IDE takes care of it automatically after selecting the device in the IDE's Tools menu.

What makes the header file so useful is that it spells out the actual names to use when writing bare metal code. I keep it open in a text editor for frequent reference. The header implements the naming rules for registers and the data stored in them, as given in the product data sheet.

The official data sheet and the silicon errata complete a minimal set of essential documentation. They can be accessed from the product page on the manufacturer's web site.

Data sheet: [https://ww1.microchip.com/downloads/aemDocuments/documents/MCU08/ProductDocuments/DataSheets/AVR64DD32-28-Complete-DataSheet-DS40002315.pdf](https://ww1.microchip.com/downloads/aemDocuments/documents/MCU08/ProductDocuments/DataSheets/AVR64DD32-28-Complete-DataSheet-DS40002315.pdf)

Errata: [https://ww1.microchip.com/downloads/aemDocuments/documents/MCU08/ProductDocuments/Errata/AVR64DD32-28-SilConErrataClarif-DS80001019.pdf](https://ww1.microchip.com/downloads/aemDocuments/documents/MCU08/ProductDocuments/Errata/AVR64DD32-28-SilConErrataClarif-DS80001019.pdf)

<h3> Other Posts in this Blog </h3> 

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
