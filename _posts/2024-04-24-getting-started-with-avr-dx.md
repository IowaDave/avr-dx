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

The official data sheet and the silicon errata complete the set of essential documentation. They can be accessed from the product page on the manufacturer's web site.

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
