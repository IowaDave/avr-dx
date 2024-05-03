# Compare AVR DA DB and DD Controllers

Here is a quick and informal comparison of 64K-flash variants in the AVR models DA, DB and DD microprocessors offered in 28-pin DIP packages. The older model AVR ATmega328P is included also, viewing the Dx lineup as potential successors to it. I limited my inquiries to those devices because I still build with breadboards and through-hole perfboards. 

I went through this exercise in mid-2023 before purchasing a supply of the DD model. Since that time Microchip has brought forth two more lines: the DU and the EA series, which are not considered here.

Each of the different series sports a unique feature.

* DD is the least expensive, costing even less than the old ATmega328P.
* DA has a peripheral touch controller (PTC).
* DB contains op amps.
* ATmega328P enjoys by far the most extensive body of open-source support among hobbyists.

The DD series has been called "the poverty level" series of the Dx family. I chose them anyway, both because they cost less than the others and because they compare favorably to the older model ATmega328P device familiar from Arduino Uno days. I list the DD model first for each feature regardless of how it compares to the other two series.

A unique feature of the DD series is to include 14- and 20-pin variants in the catalog. All three of the Dx lineups reviewed here also come in 32-, 48- and 64-pin packaging, allowing additional features that won't fit inside 28 pins. All of these products are sold in surface-mount packages. Alas, only the 28-pin models also come as DIPs.

### Information Sources

The information was gathered from the devices' respective data sheets found on the Microchip product pages as accessed at the time of writing in May 2024:

* [AVR64DA28](https://www.microchip.com/en-us/product/avr64da28)
* [AVR64DB28](https://www.microchip.com/en-us/product/avr64db28)
* [AVR64DD28](https://www.microchip.com/en-us/product/avr64dd28)

### Comparisons

#### Flash memory
* DD - 64K
* DA - 64K
* DB - 64K
* ATmega328P - 32K

#### SRAM
* DD - 8K
* DA - 8K
* DB - 8K
* ATmega328 - 2K

#### EEPROM
* DD - 256 bytes
* DA - 512 bytes
* DB - 512 bytes
* ATmega328P - 2,048 bytes

Remark: If I ever need more than 256 bytes I will reach for one of the other models, or add an external EEPROM to the circuit.

#### User Row
* DD 32 bytes
* DA 32 bytes
* DB 32 bytes
* ATmega328P - none

#### Maximum System Clock Frequency, in MHz
* DD - 24
* DA - 24
* DB - 24
* ATmega328P - 8 (16 with a crystal)

Remark: All can be clocked internally by a calibrated oscillator and externally by a crystal.

#### One 16-bit Timer/Counter type A
* All three Dx devices have one TCA.
* ATmega328P has one, 16-bit timer of a different type.

#### Three, 16-bit Timer/Counter type B
* All three Dx devices provide three instances of the TCB module.
* ATmega328P has nothing like it.

#### One, 12-bit Timer/Counter type D
* All three Dx devices provide one TCD.
* ATmega328P has nothing like it.

Remark: TCD is oriented toward controlling motors.

#### Real Time Counter
* All three Dx devices provide a 16-bit RTC that can be clocked either with an internal 32K oscillator or with an external crystal.
* ATmega328P has an 8-bit timer that does RTC only with an external crystal.

#### USART
* DD - 2
* DA - 3
* DB - 3
* ATmega328P - 1

Remark: The DD having two seemed like a nice improvement over the ATmega328P.

#### SPI
* DD - 1
* DA - 2
* DB - 2
* ATmega328P - 1

#### One TWI (IIC, I2C)
* The data sheet states that the TWI on the Dx devices "can operate simultaneously both as host and as client on different pins."
* ATmega328P TWI can operate as either host or client, but not both at the same time.

#### One, 12-bit ADC
* DD - 7 channels
* DA - 10 channels
* DB - 9 channels
* ATmega328P - makes no claims regarding the concept of a "channel". 

Remarks: 

1.The Dx series enable all of the general purpose I/I pins to function as inputs to the ADC. By contrast, ATmega328P allows only eight. 
2. The different numbers of "channels" is not as significant as it seems. The DA and DB offer a few more internally generated voltage references.

#### One, 10-bit DAC
* All three Dx devices provide a single Digital-to-Analog Converter.
* ATmega328P nas none.

#### Analog Comparator (AC)
* DD - 1
* DA - 3
* DB - 3
* ATmega328P - 1

Remark: I can see advantages for having three analog comparators in the future. But I got used to having only one AC in the ATmega328P controllers, so the DD model at least satisfies the same need.

#### Zero-Cross Detector
* All three of the Dx devices provide a single ZCD.
* ATmega328P has none.

Remark: A ZCD signals the change of an alternating voltage to negative from positive and vice versa.

#### Configurable Custom Logic Look-up Table
* DD - 4
* DA - 4
* DB - 4
* ATmega328P - none

Remark: I have *got* to learn what these things do.

#### Peripheral Touch Controller
* DD - none
* DA - 1
* DB - none
* ATmega328P - supports a Microchip proprietary touch library

#### Op Amp
* DD - none
* DA - none
* DB - 2
* ATmega328P - none

#### One Watchdog Timer
All of these devices provide a single WDT

#### Event System Channels
* DD - 6
* DA - 8
* DB - 8
* ATmega328P - none

Remark: I judged that 6 would suffice for learning purposes.

#### General Purpose I/O Pins
* DD - 22
* DA - 22
* DB - 21
* ATmega328P - 22

Remark: all of the GPIO pins on these devices can signal an external interrupt in one way or another.

#### CRCSCAN
* All three Dx devices provide a single CRCSCAN.
* ATmega328P does not have one.

#### UPDI
* All three Dx devices provide a dedicated UPDI pin for programming and debugging
* ATmega328P does not support UPDI. Programming utilizes the SPI pins.


### Conclusions

I am happy that Microchip includes a 28-pin, through-hole dual in-line (DIP) package in the Dx lineup. Nothing beats a DIP for inexpensive, rapid, hands-on prototyping.

The concept of "core-independent peripherals" in the Dx lineup opens a path into the future for the 8-bit AVR line. The idea is to bring more of a circuit inside the microcontroller, reducing the need for external parts.

At the time of writing, Arduino IDE support for the Dx lineup exists but remained a work in progress. Generous volunteers have put enough out there to get us going. See the Getting Started article, included in the list below, for information and resource links.


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


