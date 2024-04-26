# Where to Learn More about the New AVRs

Start with the DxCore repository site at Github. Here, for example, is a clear-eyed and candid assessment --- good, bad and ugly --- of both the AVR64DD28 and the way this particular core library handles it:

[https://github.com/SpenceKonde/DxCore/blob/master/megaavr/extras/DD28.md](https://github.com/SpenceKonde/DxCore/blob/master/megaavr/extras/DD28.md)

The manufacturer also provides extensive discussion of its design intentions for the new Dx series by comparing them to an earlier lineup called *megaAVR*. The following link was active when I wrote this note in April, 2024. Kindly take note that Microchip is notorious for moving files around on its web site. 

[Migration from the megaAVR to AVR Dx Microcontroller Families](https://onlinedocs.microchip.com/pr/GUID-51D4F2DF-E4D3-4379-8E03-9AAF2593C7DA-en-US-3/index.html?GUID-824D7D20-C233-4D55-BBC8-00AE9BCEB048)

Going back into history for an example, the Arduino Nano Every board is based on the megaAVR4809 controller. A web search will bring up plenty of discussion comparing the Nano Every to the older Arduino Nano board, based on the even older, ATmega328P processor.

For background and overview of all the AVRs, see also another of Spence Konde's repostories on Github, [https://github.com/SpenceKonde/AVR-Guidance/tree/master](https://github.com/SpenceKonde/AVR-Guidance/tree/master).

Like all products made by humans, these Dx chips might not do everything their manufacturer says it wants them to do. It's worthwhile to read the Silicon Errata document. Pay attention to any clarifying language in the data sheet also.

Finally, Microchip publishes a very large number of feature-focused "Technical Briefs". Sometimes I find them very helpful. I leave the finding of those documents as homework for the intrepid reader.


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

