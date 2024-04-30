# Understanding the Device Header File

Two documents spell out pretty much everything there is to know about an AVR microcontroller:

* Data Sheet (typically hundreds of pages long)
* Device Header File (typically thousands of lines of code)

These documents repay study. A code writer's task becomes much easier by keeping them open. How?

The names given to things in the data sheet can be used in a program. Copy them from the header file and paste into the code.

This article takes a detailed look inside the header file. 

Please go ahead and open the header file for the AVR64DD28 chip as it exists on Spence Konde's Github repository, [https://github.com/SpenceKonde/DxCore/blob/master/megaavr/extras/ioheaders/ioavr64dd28.h](https://github.com/SpenceKonde/DxCore/blob/master/megaavr/extras/ioheaders/ioavr64dd28.h).

You might as well also download and open the data sheet, from the product page on Microchip's web site, [https://www.microchip.com/en-us/product/avr64dd28](https://www.microchip.com/en-us/product/avr64dd28). You will need it in order to pursue this topic much farther.

### Introduction

Code controls a microcontroller by reading from and writing to specific hardware addresses, called "registers", in the "data space" of the device's memory.

The data sheet establishes a name for each address that controls the device. It identifies the information available at the address, and explains the results of writing certain values into the address.

A group of addresses that participate together to perform a certain operation is called a "module."

The header file instructs the compiler to create a named variable for each module and for each register participating in its operation, as they are named in the data sheet. Code writers can then access everything inside the controller by name.

The familiar statement from "Arduino language", 

```
digitalWrite(4, HIGH);
```

allows current to flow out of the external pin that the Arduino IDE refers to by the number 4 when the pin is in OUTPUT mode.

The same result can be obtained using names from the data sheet and the device header file.

```
PORTA.OUTSET = PIN4_bm;
```

The code tells the hardware to "set" bit number 4 in Port A, energizing the pin connected to it.

Now we dissect the statement by referring to the device header file. 


### What's In a Name?

Near line 2593 we find this definition:

```
#define PORTA                (*(PORT_t *) 0x0400) /* I/O Ports */
```

It tells the compiler to treat any mention of PORTA in your code as a PORT_t type of data structure,  stored beginning at address 0x0400 in the controller's dedicated, input/output data memory. Exactly how it performs that trick is explained in [Note A](#note-a-the-magical-point-and-shoot-syntax), at the end of this article.

This PORT_t whatsit turns out to be a C++ ```struct``` that gets defined near line 1410 in the header file. The definition lays out another list, of twenty-five items, each having its own name and purpose.

Each named item is a "member" of the struct. All members of a PORT_t struct are of the same type, ```register8_t```, which gets defined elsewhere as one, 8-bit byte of memory.

The byte named OUTSET can be accessed by combining the struct name with the member name using the "dot" operator, like this: ```PORTA.OUTSET```.

Each of the 8 bits in ```PORTA.OUTSET``` corresponds to one of the 8 pins outside the microcontroller that belong to the group named PORT A.

To this byte the code assigns the value, ```PIN4_bm```. It is a macro, defined elsewhere in the header file as:

```
#define PIN4_bm 0x10
```

The compiler will replace the text ```PIN4_bm``` with the literal constant, ```0x10```, as if the code writer had written the number instead. 

"_bm" signifies a "bit mask". The definition could be made more obvious by writing the value in its equivalent, binary form: ```0b00010000```. Thus, ```PIN4_bm``` expresses an 8-bit value in which only bit 4 is raised to logic level 1. 

It is not at all pretentious to encode the name, ```PIN4_bm``` in place of the value it represents, ```0b00010000```. Quite the opposite: it lends clarity to a code statement that targets pin number 4.

Every bit and group of bits, in every byte and group of bytes, given a name in the data sheet has a corresponding name in the header file. Search the file and ye shall find.

Copy and paste the name into your code to avoid the risk of mistyping it.

### How the Header File Is Organized

All definitions of a certain kind are grouped together and appear, by group, in the following order.

<ol type="A">
  <li>A few preliminary, generic definitions</li>
  <li><b>IO Module Structures</b>
        <p>This section defines all of the structs for the different types modules.</p>
        <p>They appear in more or less alphabetical order. </p>
      </li>
  <li><b>IO Module Instances. Mapped to memory.</b>
        <p>Here it defines variable names for each instance of all of the different structs.
        </p>
      </li>
  <li><b>Flattened fully qualified IO register names</b>
         <p>Here it starts over, again, to define unique names for each register in each, different module.</p>
         <p>Names defined here do not partake of the &lt;struct&gt;.&lt;member&gt; syntax. Rather, they define composite names, joined by an underscore, having explicit memory locations.</p>
         <p>A register can optionally be accessed directly that way, for example, either <code>PORTA.OUTSET</code> or <code>PORTA_OUTSET</code> will reach the same memory.</p>

        <p>For consistency, code writers should probably stick to just one of these two, more or less equivalent styles of address.</p>
      </li>
  <li><b>Bitfield Definitions</b>
         <p>The data sheet assigns names to the different bits in each register.</p>
         <p>Header file names for specific bits receive descriptive suffixes:<br>
             <ul>
                 <li>_bm denotes an 8-bit byte in which only that single bit is raised to 1.</li>
                 <li>_bp denotes the position, 0 through 7, of that bit in the register.</li>
                 <li>_gm denotes an 8-bit byte in which 2 or more bits comprising a "bitfield" are all raised as a group to 1.</li>          
                 <li>_gp denotes the position, 0 through, well, 6 at most, of the least significant bit of the group's bitfield.</li>
                 <li>_gc denotes a numeric constant that can be represented within the number of bits making up a certain bitfield.</li>
             </ul>
         </p>
         <p> You will see the <code>SFR_MEM8(some_address)</code> macro often in this section. It is explained in <a href="#note-b-decoding-the-sfrmem8-macro">Note B</a>, below.</p>
      </li>
  <li><b>Interrupt Vector Definitions</b><br>
       <p> These names are defined for use with the ISR() macro. That is another topic for another post.</p>
  </li>

</ol>


My critique of this organizational plan is that it sends a code writer on a scavenger hunt, throughout the file, when working with a single, particular I/O module.

An alternative plan would take each module in turn and define everything for that module together in one section.

Here is what I do about it. I create a new text file for each module when I go to work on it the first time. I retain the same outline, but copy and paste from the header file only the code pertaining to that module.

So, I have a file named PORT, another named TCB (a type of timer module), and so forth.

I accumulate these files in a folder and refer to them as needed. 

My single-module files help me find names I'm looking for faster. Another benefit of gathering them is to further my knowledge of the data sheet and the header file.


<h4 id="note-a-the-magical-point-and-shoot-syntax">Note A: The Magical Point-and-Shoot Syntax</h4>

It took years for me to understand pointers in C++ coding. One of the few satisfactions available from old age is to have lived long enough to learn something elusive. 

Now let us dissect this exotic formulation:

```
#define PORTA ( * (PORT_t *) 0x0400) /* I/O Ports */ 
```

In EEL (emotionless engineering language) we might call it DTTP syntax, acronymic for *dereferenced typecast targeted pointer*. 

The class clown in me thinks "magical point-and-shoot" sounds more fun and better describes what it does. The syntax captures a target point in memory with a single line of code.

<blockquote>
It's hard to imagine how truly magical a point-and-shoot camera (like your phone) is if you never had to meet a newspaper deadline with a manual film camera: <em>f</em>-stops, shutter speeds, flashbulbs, hours in a darkroom hoping for the best &mdash; but I reminisce...
</blockquote>

C++ statements like that are best unravelled from right to left. Let's take it apart.

* ```/* I/O Ports */``` is just a comment. Ignore it.

* ```0x0400``` is the target address in the data memory of the controller. This one is specified in **Table 9-1 Peripheral Address Map** of the data sheet for the I/O module named PORTA. It is just literally copied over from there into the header file.

* ```(PORT_t *)``` is C/C++ syntax for *typecasting* the value appearing to the right of it. The compiler sees two things when it looks inside the enclosing parentheses to ascertain the code writer's intentions.<br><br>  1. The asterisk ```(...... *)``` in this position typecasts the target address as a pointer. For an 8-bit AVR controller, a pointer is a 16-bit, unsigned number representing an address in memory.<br><br>  2. ```(PORT_t *)``` predicts that the block of memory starting at that address will conform to a struct of type PORT_t.<br><br>The compiler makes no attempt to fact-check this declaration; it believes the code writer and will access the memory accordingly.<br>
<br>If you stop there and assign the result to the variable named PORTA it would return only the number, 0x0400.<br><br>However, what we want is to *see through the pointer into the contents* of the memory at that address.

* To our aid comes now the leftmost asterisk. It *dereferences* the pointer.<br><br>Dereferencing a pointer exposes the contents of the memory to which it points.

* The whole kabob gets enclosed in the outermost pair of parentheses, but these serve only to make sure the entire expression gets evaluated before being assigned to anything.

* Finally, ```#define PORTA``` assigns the *dereferenced* pointer to the name ```PORTA```. 

It is *almost the same thing* as defining PORTA as a global variable, which would look like this:

```
PORT_t PORTA;
```

but with one important difference. Doing it that way would assign to PORTA a block of meaningless memory at some arbitrary address in SRAM chosen by the compiler. By contrast, the magical point-and-shoot syntax opens a treasure chest full of powerful hardware registers found at a definite address, chosen by the code writer, located deep down inside the device.<br><br>How do we know what's in there? The data sheet tells us; that's how. See section **18.4 Register Summary - PORTx** on page 171 and the details for each register that follow on pages 172-193.

<H4 id="note-b-decoding-the-sfrmem8-macro">Note B: Decoding the SFR_MEM8() Macro</h4>

If you read through Note A, above, then this one is easily explained.

```SFR_MEM8(some_address)``` disentangles into a way of writing the point-and-shoot syntax designating a hardware memory address for assignment to an 8-bit variable. 

```(*(uint8_t *)(some_address))```

The device header file does not contain this definition. It comes instead from a file named [sfr_defs.h](https://www.nongnu.org/avr-libc/user-manual/sfr__defs_8h_source.html) that is included with the AVR-GCC compiler at the heart of the Arduino IDE. AVR-GCC is a Free Software project hosted on the NonGNU web site at [https://www.nongnu.org/avr-libc/](https://www.nongnu.org/avr-libc/).

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

