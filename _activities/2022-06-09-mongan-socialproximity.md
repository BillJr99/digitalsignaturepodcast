---
layout: activity
title: "Social Vicinity with the Microbit"
permalink: /activities/socialvicinity

gradelevel: secondary
category: microbit
subject: computing

gitrepo: https://github.com/billjr99/social-vicinity 

authors: "William M. Mongan"
date: 2022-06-09

summary: "In this activity, we will use the radios of two or more Micro:bit devices to calculate how long we've been around each of our friends."

thumbnail: "https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Social-network.svg/640px-Social-network.svg.png"
---

The Micro:bit has a Bluetooth radio that can send and receive messages to and from other Micro:bit devices.  In fact, it's the same radio that it uses to send and receive programs from your computer or phone to run on the Micro:bit!  We'll use these radios to send messages out to other Micro:bit devices, and when we can &quot;hear&quot; one of those messages, we'll make a note of it.  Whichever radios we've received the most of these messages from are the ones we've spent the most time with.  

Radio frequency waves are a bit like the light from a flashlight.  If everyone had a flashlight, they could turn them on and off and others would see the light.  But if you hid behind a wall or a door, only the people on your side would see your light.  Radio waves use different frequencies of light that can seep through walls and doors, but eventually they will fade out and you won't be able to see them if you're too far away.  

### On Start
In the <strong>on start</strong> block, let's set up a few things we'll need to keep track of.  We want to know how many messages we've received from each of our friends.  To do this, we'll make a table, or an <strong>array</strong>.  At first, we haven't made any friends, so we'll set the number of messages we've received to 0.  Let's create an array of ten items, each one containing the value 0.

Next, we'll set our radio number.  Create a variable called <strong>radio_number</strong>, and we'll initially set this to 0.  We want each person to have their own number, so we'll need a way to change that for each person later.  We'll come back to this.

Finally, set the <strong>radio group</strong> to the same number as everyone else.  That's like setting the channel on a walkie-talkie: this allows everyone to communicate on the same frequency and hear one another's messages.  I used the number 1 for this, but as long as everyone picks the same value, that's good.

### Now What?
Well, we want to keep sending out messages and see who hears it.  We also want to do something when we hear a message, so we'll need blocks for this.

Since we're sending messages over and over, we'll put that into a <strong>forever</strong> block.  Let's send our radio number variable: use the <strong>radio send value X = Y</strong> step, and send the title <strong>hello</strong> and the value of the <strong>radio_number</strong> variable.

In a <strong>on radio received X = Y</strong> block, we'll update that we heard a message from someone.  But which friend's message did we hear?  That's the point of sending the radio number.  If we get a message from friend 3, we'll add 1 to the third column of our table.  Set the array variable at <strong>value</strong> to the value of the array at <strong>value</strong> plus 1.  

I added an <strong>if</strong> statement here to only make this update if the title of the message is <strong>hello</strong>.  That's the only type of message we're sending, so this is really optional, but I thought it would be nice to use the title somewhere!  Feel free to add this if you like.

### Setting our Radio Number
If we type a radio number variable value into our program, it will be the same for everyone.  How could we allow everyone to set their own number after it starts running?  One idea is to add 1 to the radio number every time you press the A button.  Each person can press A a different number of times.

Add a block <strong>on button A pressed</strong>, change the <strong>radio_number</strong> variable by 1, and <strong>show number</strong> to display that value on the screen (so you know what it is!).

This would increase the value of <strong>radio_number</strong> forever, but if you're only keeping track of 10 friends (since the array size is 10 from our <strong>on start</strong> block earlier), it would be helpful to go back to 0 when you get to 10. To do this, you could also set <strong>radio_number</strong> to the value of <strong>radio_number</strong> after dividing by 10.

### Displaying the Results
We can't display every value of an array on the little LED display of the Micro:bit.  But we can use this idea of changing a number every time we press a button to display a specific value from the table.  Add a block so that when the B button is pressed, you change a variable called <strong>friend_index</strong> (you can set this to 0 in the <strong>on start</strong> block) by 1.  I suggest setting <strong>friend_index</strong> to the remainder after dividing itself by 10 here as well so it wraps back around to 0 if you press B enough times (just like we did with <strong>radio_number</strong> and the A button).  Then, show the value of the <strong>friends</strong> array at <strong>friend_index</strong>

### The Finished Project

Here's my finished product, available at [https://www.billmongan.com/social-vicinity/](https://www.billmongan.com/social-vicinity/):

<p align="center">
<img style="max-width:100%;" alt="The finished hide and seek project in the Microbit Makecode" src="https://github.com/billjr99/social-vicinity/raw/master/.github/makecode/blocks.png">
</p>

### Follow-Up

#### Tracking Time with More Friends

How might you change this program to track more than 10 friends?

#### From Message Count to Time

Here, we counted the number of messages we received from each friend.  How might you convert this to time?  You might time how long you were around someone, and divide the number of messages you heard by that time to get what's called the radio's transmit rate.  Then, you can divide the count by that value to estimate the time in seconds.

When you press the A+B buttons together, send this time as a different message title (other than <strong>hello</strong>) to your friend, and have them display that time.  How would you do this?