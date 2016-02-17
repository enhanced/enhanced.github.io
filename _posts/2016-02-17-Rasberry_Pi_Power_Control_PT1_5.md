---
layout: post
title: "N0PKT Rasberry Pi Power Control Part 1.5"
date: 2016-02-17 00:00:00
tags: 
 - "amateur radio"
 - repeater
 - n0pkt
 - "rasberry pi"
 - electronics
description: Updated info about a better GPIO library and set of utilities.
---

## What this post is all about
This is a minor update to yesterdays post.  After I had done my testing and written the post I decided to look for better ways to control the GPIOs without requiring that the executing user run the commands as *ROOT*.  Through the magic that is google I stumbled upon the [pigpio set of libraries and utilities](http://abyz.co.uk/rpi/pigpio/index.html).

### Software
In the previous post I detailed the usage of the Python RPi.GPIO library.  However, I was pretty dissatisfied with the fact that the Python needed to be executed with *ROOT* permissions, as this causes a significant security risk.  As noted above: a bit of googling produced the fruitful [pigpio](http://abyz.co.uk/rpi/pigpio/index.html).  The benefit of pigpio (other than the fact that it's cleaner) is that your python itself will no longer have to run under a root context.  There will be a daemon (still running under a root context) that will accept commands via socket.  In this way you can also _remotely_ control GPIOs on other RasPis.

>I am running Debian as my base distribution, so these steps may be specific to Debian (though they shouldn't be). I also assume that you have the appropriate dependencies installed to build from source.
{: .note}

Step 1 - Get the tarball and install. 
{% highlight bash %}wget abyz.co.uk/rpi/pigpio/pigpio.zip
unzip pigpio.zip
cd PIGPIO
make
sudo make install
sudo python setup.py install{% endhighlight bash %}

>This is compiling on a RasPi, so keep in mind that it may take several minutes to build - don't panic!
{: .note}

### Code and Initial Testing
Some minor changes to test the boards in this section - everything other than the code is going to be similar.

Launch the daemon that controlls the GPIOs:
{% highlight bash %}sudo pigpiod
ps aux | grep pigpiod
root     17722  5.6  0.1  10864  1288 ?        SLsl Feb16  54:49 pigpiod{% endhighlight bash %}

Quick Test - Launch a Pyton interactive shell (NOT AS ROOT) and issue some verification and testing commands.  By issuing the following commands, assuming you are using the same RasPi and Relay Board as I am, you should see the LED and hear relay1 (IN1) physically switch over to the NO position when you set output to LOW and back to NC when you swithc to HIGH.
{% highlight python %}$ python
Python 2.7.9 (default, Mar  1 2015, 13:48:22)
[GCC 4.9.2] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import pigpio
>>> p1 = pigpio.pi()
>>> p1.write(18,0)
0L
>>> p1.write(18,1)
0L
>>> exit(){% endhighlight python %} 

>You can also see that there is a response from issuing these commands, useful for error handling etc...
{: .note}

I mentioned that this specific setup operates in reverse of what I expected.  Normally, or normally according to google searches, _OBJ.write(18,0)_ will turn *OFF* the relay.  However, in the case of the noted relay board and raspi model it activates the NO side of the relay.  Here are a few scripts that I took from [GitHub](https://github.com/fixedd/RPi_Relay_Interface) and modified to work with this specific library, daemon, wiring and board model(s).

Turn *EVERY* relay on:
{% highlight python %}#!/usr/bin/python
import pigpio

relay_pins = {'one':18, 'two':23, 'three':24, 'four':25}
pi1 = pigpio.pi() # initialize a local pi object

for relay_pin, board_pin in relay_pins.iteritems():
        pi1.write(board_pin, 0) # Set output to low {% endhighlight python %}

Turn *EVERY* relay off:
{% highlight python %}#!/usr/bin/python
import pigpio

relay_pins = {'one':18, 'two':23, 'three':24, 'four':25}
pi1 = pigpio.pi() # initialize a local pi object

for relay_pin, board_pin in relay_pins.iteritems():
        pi1.write(board_pin, 1)  # Set output to high{% endhighlight python %}

Cycle through 18,23,24,25 in a cylon style, ending with all in NC position:
{% highlight python %}
#!/usr/bin/python
import pigpio
import time

sleep_time = 0.05

# Define what pins we will be using, this is always using BCM when we use pigpio
relay_pins = {'one':18, 'two':23, 'three':24, 'four':25}

# Create an object referencing a local Pi
pi1 - pigpio.pi()

# Function to turn off and on the GPIOs
def float_pin(off, on):
        if (on != None):
                pi1.write(on, 0)
                time.sleep(sleep_time);
        if (off != None):
                pi1.write(off, 1)
                time.sleep(sleep_time);

# Iterate trough each of the relay pins cycling on and off looking all cylon!
for b in xrange(2):
        float_pin(None               , relay_pins['one']  )
        float_pin(relay_pins['one']  , relay_pins['two']  )
        float_pin(relay_pins['two']  , relay_pins['three'])
        float_pin(relay_pins['three'], relay_pins['four'] )
        float_pin(relay_pins['four'] , relay_pins['three'])
        float_pin(relay_pins['three'], relay_pins['two']  )
        float_pin(relay_pins['two']  , relay_pins['one']  )
        float_pin(relay_pins['one']  , None               )
{% endhighlight python %}

### Final Thoughts
Even with setups like this in potentially closed systems one should have security and extensibility kept well in-mind.