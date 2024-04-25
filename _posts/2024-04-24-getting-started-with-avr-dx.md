---
description: About AVR Dx Coding
---

# Getting Started with AVR Dx
Begin exploring the AVR64DD28 microcontroller

The day will surely come to say goodbye to our trusty, old, familiar ATmega328P chips we learned to love on Arduino Uno development boards.

My preference in a replacement includes availability of a 28-pin DIP package. Microchip offers a number of such products in its modern, "Dx" lineup. I chose the AVR64DD28 for further study.

Its features compare very favorably to those of the old 328s. See the [data sheet on the manufacturer's web site](https://ww1.microchip.com/downloads/aemDocuments/documents/MCU08/ProductDocuments/DataSheets/AVR64DD32-28-Complete-DataSheet-DS40002315.pdf).

 

<ul>
  {% for post in site.posts %}
    <li>
      <h2>
        <a href="{{site.baseurl}}{{ post.url }}"       
        {% if post.title == page.title %}
           style="color: black;"
        {% endif %}>{{ post.title }}
        </a>
        
        {% if post.title == page.title %}
          &nbsp; << You are here.
        {% endif %}
        
      </h2>
    </li>
  {% endfor %}
</ul>
