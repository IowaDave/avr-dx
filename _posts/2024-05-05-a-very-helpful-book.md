---
description: discusses Tom Almy's book Far Inside the Arduino Nano Every Supplement
title: "A Very Helpful Book by Tom Almy"
---

# A *Very* Helpful Book by Tom Almy

The AVR Dx microcontrollers share many attributes with a previous lineup that Microchip calls "the megaAVR series." One of those is the ATmega4809 found on the Arduino Nano Every product.

Tom Almy, an electrical engineer, wrote a nice book about the ATmega4809, called *Far Inside the Arduino: Nano Every Supplement -- (The ATmega4809 unleashed)*. At the time of writing in May, 2024, it was available for purchase on Amazon-dot-com.

His book comes with access to a rich set of example programs and code-writing aids.

Skimming through a datasheet for the ATmega4809, I see the two devices appear to embody many of the same internal peripherals.

Tom's book goes "far inside" the 4809 chip to reveal, explain and exploit features that the regular Arduino core library might not make readily available.

It turns out that many features of the ATmega4809 are found also in my AVR64DD28. Bravo! Tom's book becomes a tool in the hand for someone digging into the AVR Dx family of microcontrollers.

Differences exist between the two device families, of course, but not many. The similarities make this book deserving of consideration for anyone seeking better understanding of an AVR Dx.

### A Second Look at the ATmega4809

The ATmega4809 is available in a 40-pin DIP package for us through-hole, breadboard prototypers. 

Having 12 pins more, compared to 28-pin DIP packages offered in the Dx lineup, brings more resources to a project that might need them. Examples include more timer/counters, USART serial interfaces and event system channels. The extra dozen I/O pins could prove handy as well.

I feel sure that lessons learned about Dx chips would carry over beneficially toward understanding when and how to make use of a 4809 instead.

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