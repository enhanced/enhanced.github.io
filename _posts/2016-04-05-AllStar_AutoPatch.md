---
layout: post
title: "Setting up AutoPatch and Reverse AutoPatch"
date: 2016-04-04 00:00:00
tags: 
 - "amateur radio"
 - repeater
 - n0pkt
 - "rasberry pi"
 - electronics
description: How to make autopatch and reverse autopatch work in an allstar node.
---

## Documentation and Understanding
I am hoping that someone finds this post as useful as I might have.  Setting up the reverse autopatch was possibly the most difficult (or frustrating) part of this entire repeater project thus far.  One thing that I hope to help rectify going forward is the sparse documentation that exists for the [AllStar project](http://www.allstarlin.org).  Please note that I am not trying to disparage the amazing work that Jim and the rest of the team have done, I certainly understand that they put a substantial amount of effort and development in to get the projet to where it is.  Rather, I am noting that this is my meager contribution in return and that ideally I will contribute more back to the project.

## The Nuts and Bolts
This post is intended to be a quick and dirty (how I did it) guide, with a little explanation along the way, of how to get autopatch and reverse autopatch functioning.  In order to get this working I took a good while (few days) putting together a basic grasp on how asterisk [dial-plans](https://wiki.asterisk.org/wiki/display/AST/Creating+Dialplan+Extensions) and their logic flow works.  You will find that the [stanza](http://docs.allstarlink.org/drupal/node/24) sections will refer *via goto* statements to other stanzas and the like.  My mistake was that I changed names and made my own stanzas before I understood how it all tied together **FACEPALM**

### Autopatch *And in Reverse*
For my configuration I used a Google Voice account without any Credit Card or payment info attached so that I didn't have to worry about toll fraud.  I also used a service that acts as a SIP gateway to Google Voice.  Information about the gateway that I used can be found at [https://simonics.com/services/](https://simonics.com/services/).  I elected to us this service rather than setting up my own gateway simply to reduce the complexity of what would need to be maintained going forward.  There are other options out there, and a bit of googling should get you sorted.

#### sip.conf
There are two components that need to go into the *sip.conf* file.  One is the register line, and the other will be the stanza that the register line refers back to.  This will be the configuration that allows us to both receive and place calls via our SIP peer.

Register example:

* ACCOUNT is the account user from simonics GV Gateway
* PASSWORD is the password for this user
* STANZAREFERENCE refers to the stanza detailed below
{% highlight text %}
register=ACCOUNT:PASSWORD@STANZAREFERENCE/NODE
{% endhighlight text %}

Stanza example:

* ACCOUNT is the account user from simonics GV Gateway
* PASSWORD is the password for this user
* Make note of the "context" value - we will use it later
* There may be some options that are not needed, but these are what I used to make it happen, YMMV
{% highlight text %}
[STANZAREFERECE]
type=peer
allowguest=yes
nat=yes
autocreatepeer=yes
insecure=port,invite
username=ACCOUNT
secret=PASSWORD
host=gvgw.simonics.com
qualify=no
allow=ulaw
allowexternaldomains=yes
context=custom-gv-answer
{% endhighlight text %}

Also note that *chan_sip.so* needs to be set to load in your modules.conf

#### extensions.conf
The stanza names in this section are important, this is where I broke it all for myself and then spent several days working backward to understand how stanzas and dial-plans work.  These stanza names are referred to elsewhere by *context=* and *goto* lines and this is what comprises the logic flow, in a simplistic way.

There will be only two stanzas that we will add, the [pstn-out] is referred to in a few other spots and so must retain its name *or change it everywhere that it is called in extensions.conf*.  The other is [custom-gv-answer] and simply must match the *context=* line that is in the sip.conf.

[pstn-out] *autopatch*

* STANZAREFERENCE refers back to the same name in sip.conf
    - This tells asterisk where to send a call matching the NXXNXXXXX pattern for autopatch calls
{% highlight text %}
[pstn-out]
exten => _NXXNXXXXXX,1,Dial(SIP/STANZAREFERENCE/\${EXTEN})
exten => _NXXNXXXXXX,2,Congestion
{% endhighlight text %}

[custom-gv-answer] *reverse autopatch*

* MYNODE is your Allstar node number, this is important
{% highlight text %}
[custom-gv-answer]
exten => _X.,1,Ringing
exten => _X.,n,Answer()
exten => _X.,n,Rpt(MYNODE|Rrpt/node:NODE:rpt/in-call:digits/0:PARKED|120) 75
exten => _X.,n,Hangup
{% endhighlight text %}

#### features.conf
This file should look like this by default, but just in case.  This is what tells asterisk what slots to put the calls in, starting at 701.
{% highlight text %}
;
; Sample Call Features (parking, transfer, etc) configuration
;

[general]
parkext => 700                  ; What extension to dial to park
parkpos => 701-720              ; What extensions to park calls on. These needs to be
                                ; numeric, as Asterisk starts from the start position
                                ; and increments with one for the next parked call.
context => parkedcalls          ; Which context parked calls are in
{% endhighlight text %}

#### modules.conf
Simply make sure that *app_parkandannounce.so* is set to load.

### Putting it all together
Assuming all of your configuration is good and you are registering with the gateway as a SIP peer, reverse and forward autopatch should work using whatever autopatchup and autopatchdn DTMF function is defined in your *rpt.conf*  Mine are defined as:
{% highlight text %}
6 = autopatchup,noct = 1,farenddisconnect = 1,dialtime = 20000  ; Autopatch up
0 = autopatchdn                                                 ; Autopatch down
{% endhighlight text %}
Given this *and reloading asterisk* I can key and press *6 on my HT and hear a dialtone, at that point I can key again and dial the 10 or 11 digit phone number that I want to call and shazaaam it should work. To hangup a simple key *0 will get it done.

To receive a all, you should be able to call your GV number, the repeater will then announce that an incoming call is occurring and tell you the extension (default is 0701), in that case *60701 will pick the call up from park and *0 again to disconnect and hang up

### A few fun notes
Asterisk provides some pretty solid debugging (sometimes more and sometimes less than you need).  Make use of them:

* core set verbose 4
* sip set verbose 4

And with that, I'll bid you all 73s!

N0PKT
