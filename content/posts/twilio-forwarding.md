---
title: "Simple Twilio Forwarding"
date: 2021-01-23T18:23:21-05:00
description: A simple way to use a Twilio phone number and forward to other numbers. It was useful for an online store that I setup.
draft: false
tags: [twilio, ecommerce, startups, voice]
cover: https://dev-to-uploads.s3.amazonaws.com/i/ja7x2bixvdahr2s7fmsd.jpg
author: vijay
---

I was setting up an online store for a bunch of friends through Shopify. We setup chat channels using Messenger and Shopify Ping, but we still wanted to have a phone number in case the customers wanted to talk. The online store did not have a physical store and did not yet want to invest in a help desk setup.

We came up with a simple solution through a Twilio phone number - that when called simultaneously rang the mobile phones of the partners.

## Requirements

1. A Phone Number
2. Initial Greeting
3. Simultaneous Dialings
4. Record A Message

## A Phone Number

Twilio has been super great being a developer friendly service. We chose Twilio to purchase a virtual phone number. Some of the choices include region specific phone number or a toll free number. You can purchase once you sign up for free at [twilio.com](https://www.twilio.com)

Purchase a number [here](https://www.twilio.com/console/phone-numbers/search)

![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/wdfilm2ypx4kxzzo9g1v.png)

* Search for a number of your liking
* Choose Voice capability

## Initial Greeting

When any customer calls the above phone number, you could program it in multiple ways. We went with creating a simple TwiML script.

* Configure your active numbers [here](https://www.twilio.com/console/phone-numbers/incoming)
* Configure the Voice & Fax section by choosing *TwiML Bin* for *A call comes* in option 

![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/nb1djhsncpivfmv0mpgz.png)

* Press the + on the right to create a new TwiML script (full script in the end)

Voice greeting can be set using the **<say>** block. 

```
    <Say voice="man">
      Welcome to online store dot com - please wait while we connect your call.
    </Say>
```

`<Say>` block speaks the text in a computerized (yet human-like) voice. There are multiple options for `voice` available.

## Simultaneous Dialings

The next requirement was to have the incoming call result in calling a few of the partners and the most available person would pick up and connect the call.

This code block dials the numbers in the values.

```
    <Dial timeout="20" record="true" callerId="+19000000000">
      <Number>+16000000000</Number>
      <Number>+14000000000</Number>
    </Dial>
```

Parameters `timeout`, `record`, and `callerId` are passed to the Dial block. 

* `timeout` sets the number of seconds the numbers below should ring before going to the next block
* `record` sets the setting to record or not
* `callerId` sets the phone number from which the Numbers below should receive the calls. We set to a Twilio verified number, as we wanted to partners to know that this is a call for the online store. You will not be able to specify a number that has not been verified in Twilio

`<Number>...</Number>` blocks are put inside `<Dial>` block to dial simultaneously.

## Record A Message

At instances where all the few partners are able to receive the call, we wanted the customer to record a message to replay and address the needs.

This code block achieves the requirement

```
    <Say voice="man">
      All our representatives are busy. Please leave a message and we will contact you back. Thank you.
    </Say>
    <Record maxLength="20" />
```

* Before recording the voicemail, we put a prompt that everyone is busy and to leave a message.
* Record block records upto 20 seconds of voice

## Conclusion

TwiML script is executed top down one block at a time. `<response>` block will be executed as response to the incoming call. Here is the full code.

```
<?xml version="1.0" encoding="UTF-8"?>
<Response>
    <Say voice="man">
      Welcome to online store dot com - please wait while we connect your call.
    </Say>
    <Dial timeout="20" record="true" callerId="+19000000000">
      <Number>+16000000000</Number>
      <Number>+14000000000</Number>
    </Dial>
    <Say voice="man">
      All our representatives are busy. Please leave a message and we will contact you back. Thank you.
    </Say>
    <Record maxLength="20" />
</Response>
```

Hope this helps you in a similar case. Please let me know if there is a better way to fulfil the requirements.

Also, here is a [referral link](www.twilio.com/referral/xlaXwS) if you would like to try out Twilio.

