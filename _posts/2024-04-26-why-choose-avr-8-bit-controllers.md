# Why Choose AVR 8-bit Controllers?

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

