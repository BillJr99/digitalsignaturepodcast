---
layout: activity
title: "Rock Paper Scissors and Radio Protocols with the Microbit"
permalink: /activities/rockpaperscissorsprotocol

gradelevel: secondary
category: microbit
subject: computing

gitrepo: https://github.com/billjr99/rock-paper-scissors-protocol

authors: "William M. Mongan"
date: 2022-07-07

summary: "In this activity, we will develop a protocol for matching up with a player and playing a game of rock-paper-scissors using the Bluetooth radio."

thumbnail: "https://upload.wikimedia.org/wikipedia/commons/3/37/Radio_Station_2.jpg"
---

### Getting Started: Matching up with a Player
The first thing we'll want to do is plan how to connect two players with one another.  To get a sense of how this could work, walk around the room and find a partner to play rock-paper-scissors with.  Only play with one other person, and don't match up with someone else while you're playing with that person.  How did you know that someone was playing as opposed to looking for a partner?  What would have made it easier to match up?

One way you could make this easier is by saying something: perhaps every few seconds, you might say "I need a partner" until you find one.  That might feel a little awkward!  Instead, you could write a symbol on a piece of paper, like a question mark, that shows that you still need a partner.  When you find one, flip the paper over to show the other symbol (perhaps an exclamation point).  These symbols represent your **state**: what you're looking to do right now.  Everyone else can see your state, and match up with you.

Let's try this with the paper technique.  Once you find a partner, flip the paper over to reveal the exclamation point.  But what does the other person do?  They should also flip their paper over and show the exclamation point, to show that they are also matched up.  This is called a **three-way handshake**.  What do you think the three steps are?  Here's the breakdown:

1. Find a partner that is not yet paired up, and let them know you're ready to pair up with them.
2. Once they contact you, let that person know that you're also ready to pair up with them.  Now, you're connected with the partner that just contacted you.
3. They'll receive your response, and now you're fully paired up.

In steps 1 and 2, a potential partner finds you and connects to you.  In steps 2 and 3, you meet that partner, and connect with them.  All together, there are 3 basic steps.  This is the basic foundation for the way that computers connect to one another to communicate on the Internet.

#### Implementing a Three-Way-Protocol on the micro:bit
You might have guessed that the sheet of paper will correspond to a variable in our micro:bit program!  Let's create a numeric variable called `protocol_state`.  Initially, we'll set it to 0, which will be like our question mark in `on start` (meaning we're looking for a partner).

##### Finding a Partner
In the `forever` loop, let's call out for a partner until we find one.  How will we know when we've found one?  We can set the state to 1 once we're paired up.  Also, we need a way to identify ourselves to our partners.  We'll create another variable called `my_id`, which we can also set to 0.  That's going to be a problem, because these numbers need to be unique so that we know who our partner is.  We'll provide a button to set this number later.

So, as long as the state is 0, we'll keep sending a radio message.  This message can really be anything, but let's send one word to indicate what we're asking for, and our ID number, with a space in between.  We'll call this a `request` message:

```python
def on_forever():
    if protocol_state == 0:
        radio.send_string("request" + " " + convert_to_text(my_id))
```

To save battery, let's add a `pause` statement in that loop so that we only send this message every second or so.  

##### Receiving a Request from a Potential Partner
What should we do when we receive this message?  When we receive a string, we should check for the first word, and see if it's a request.  So far, that's the only type of message we can receive (but there might be others later!).

If the word `request` is in the message, then we know that the second word is the ID number of the other player that we can connect with.  We can use the `split` function to split up a message by spaces, and get the second item to represent the ID number (this wil be index 1, since index 0 is the first word: `request`).

There are a few things to think about, though.  First, we wouldn't want someone "cutting in" on our connection.  So, we should only connect with someone that is also looking for a partner.  How will we know this?  Well, only respond to someone's request if your own `protocol_state` is 0 (meaning that you're looking for a partner).  

If we get a message from someone seeking a partner, and our state is 0 (meaning we're looking for a partner), we'll respond with another message.  We'll call this a `response` message.  

Before we send this message, let's make sure that the ID of the person is not the same as our own ID.  It shouldn't be, of course, but since we set everyone's to 0 for now, they're all the same!  We still need a way to give everyone an ID.  For now, let's just ignore any messages sent from someone with the same ID as ourselves.  

Otherwise, we'll create a new variable called `sender` that holds the ID of our new partner.  The partner's ID was the second word in the request message that we received, so that's split position 1.  They'll need to know who we are, so we'll include our own ID in the response.  But what if the whole room sees that message?  Everyone else should ignore it, since we're only partnering up with one person.  Let's address our response to a specific ID: namely, the ID of the person that sent us the request.  So, in all, we'll include the sender's ID and our own ID.  Now that we're potentially partnered with someone, we'll change our state from 0 to 1, so that we don't abandon them to partner with someone else!

```python
def on_received_string(receivedString):
    global sender, protocol_state
    if receivedString.includes("request"):
        if protocol_state == 0:
            sender = parse_float(receivedString.split(" ")[1])
            if sender != my_id:
                protocol_state = 1
                radio.send_string("response" + " " + convert_to_text(sender) + " " + convert_to_text(my_id))
``` 

##### Receiving the Response Message
What should we do when we receive the `response` message?  We should:

1. Make sure our state is 0, so that we're looking for a partner, just like before
2. Make sure the message is addressed to our ID, so that it's intended for us

If so, this brings us to the third step of the three-way-handshake, which is letting the original person know that we are indeed partnered up.  This is important in case someone is being asked to partner with two people at the same time.  We'll pick one and acknowledge them.  Actually, we aren't really picking, we're just going to reply to the person whose message we saw first.  We'll call this message an `acknowledge` message.  Just like before, we'll change our state to show that we're fully connected.  Let's call this state number 2.  We'll pull our the sender value from the message we just received, just like before, and store that in the `sender` variable.  If you look at the original `response` message, you'll see that the sender is the third word in the message, or split index 2.

```python
def on_received_string(receivedString):
    global sender, protocol_state
    if receivedString.includes("request"):
        # same as before...
    elif receivedString.includes("response"):
        if protocol_state == 0 and parse_float(receivedString.split(" ")[1]) == my_id:
            sender = parse_float(receivedString.split(" ")[2])
            if sender != my_id:
                protocol_state = 2
                radio.send_string("acknowledge" + " " + convert_to_text(sender) + " " + convert_to_text(my_id))
```

##### Receiving the Acknowledge Message
We just sent a new kind of message (the `acknowledge` message), so we will need to do something when we receive it.  When we receive the `acknowledge` message, it means we have a new partner!  Which state number means that we have a partner?  We used state number 2 from the previous step, so that's what we'll use here, too.  To be safe, we'll check for a few rules just like we always do:

1. We are in the middle of a three-way-handshake (that is, our current state is 1)
2. The message is addressed to our ID
3. The message is being sent from the partner we just paired with in the prior step of the three-way-handshake (we set their ID in the `sender` variable earlier

```python
def on_received_string(receivedString):
    global sender, protocol_state
    if receivedString.includes("request"):
        # same as before...
    elif receivedString.includes("response"):
        # same as before...
    elif receivedString.includes("acknowledge"):
        if protocol_state == 1 and parse_float(receivedString.split(" ")[1]) == my_id and parse_float(receivedString.split(" ")[2]) == sender:
            protocol_state = 2
```

#### Playing the Game
That took a lot of steps!  Imagine what we just did, though: we created a **protocol** by which lots of micro:bit devices all shouting out for partners in a room can pair up with someone.  Now that we know the ID of our partner, and we know for sure that they have us as a partner, we can communicate with them directly by addressing messages to their ID.  By programming ourselves to ignore other messages, we'll use the same radio waves in the room as everyone else, but only communicate with our own partner!  It's kind of like having a conversation with someone at a party: there's a lot of background noise, but once you strike up a conversation with someone, you're really only speaking and listening to them.  The best part is that this protocol works for any program - it's not specific to this game.  We're really only playing rock-paper-scissors so that we have something to do to show that our protocol worked.  

##### Picking our Guess
The B button can cycle through rock, paper, and scissors by selecting the numbers 0, 1, and 2.  To do this, we'll set a variable called `rps` (for rock-paper-scissors) to `(rps + 1) % 3`.  I'll use the B button to cycle through the guess:

```python
def on_button_pressed_b():
    global rps
    rps = (rps + 1) % 3
    if rps == 0:
        basic.show_icon(IconNames.SMALL_HEART)
    elif rps == 1:
        basic.show_icon(IconNames.CHESSBOARD)
    else:
        basic.show_icon(IconNames.SCISSORS)
input.on_button_pressed(Button.B, on_button_pressed_b)
```

##### Sending our Guess
To play, we'll send a message called `play`.  The `play` message should contain the ID number of the partner we're sending to (just like any message should, so that we can communicate only with our partner), and a number indicating whether we're playing rock (0), paper (1), or scissors (2).

When we press the A+B buttons together, we'll send our guess to the other player through the `play` message.  What state should we be in to send our play?  We should be paired up with a partner, so that's state number 2.  Like always, we'll include our partner's ID, our ID, and our guess.

```python 
def on_button_pressed_ab():
    global protocol_state, sender, my_id
    if protocol_state == 2:
        radio.send_string("play" + " " + convert_to_text(sender) + " " + convert_to_text(my_id) + " " + convert_to_text(rps))
input.on_button_pressed(Button.AB, on_button_pressed_ab)
```

##### Receiving our Guess
When we receive a `play` message, we can compare it with our own guess (if we've made one yet) to see who won the game.  How can we know if we've guessed yet?  Let's update our state value!  When should we do this, and to what value?  We need a new state value, so how about 3.  As for when to do it, we made our guess when we pressed A+B together, so we'll update the state there:

```python 
def on_button_pressed_ab():
    global protocol_state, sender, my_id
    if protocol_state == 2:
        radio.send_string("play" + " " + convert_to_text(sender) + " " + convert_to_text(my_id) + " " + convert_to_text(rps))
        protocol_state = 3
input.on_button_pressed(Button.AB, on_button_pressed_ab)
```

Now, let's handle the `play` message in our `on_received_string` function, where the other messages are.  As always, we'll make sure the message is addressed to us and from our partner.  We'll also check that we're in protocol state 2 (if we haven't played yet), or 3 (if we have already sent our play).  We'll save our opponent's play as a new variable called `opponent_rps`.

```python
def on_received_string(receivedString):
    global sender, protocol_state, opponent_rps
    if receivedString.includes("request"):
        # same as before...
    elif receivedString.includes("response"):
        # same as before...
    elif receivedString.includes("acknowledge"):
        # same as before...
    elif receivedString.includes("play"):
        if protocol_state >= 2 and parse_float(receivedString.split(" ")[1]) == my_id and parse_float(receivedString.split(" ")[2]) == sender:
            opponent_rps = parse_float(receivedString.split(" ")[3])       
```

##### Determining when Both Players have Played
When should we check for a winner?  This can be a little tricky, because you want to check after you have played and after your opponent has played.  Depending on who goes first, this can either be when you receive your partner's guess as a `play` message, or when you send your guess when you press the A+B buttons.  We'll check in both places.  We know that state number 3 means that you have sent a guess.  We'll add another state, state 4, to mean that you have received your opponent's guess.  So, when you press A+B, if you find that your state is 4, you should go ahead and check for a winner; otherwise, set your state to 3 and wait.  Also, when you receive a `play` message, if your state is 3, check for a winner, and otherwise set your state to 4.  Draw this out as a flowchart to see how it will work!

Here's our new `play` message code to handle this new state.  We'll create a function called `check_play()` that determines the winner, so that we don't have to fill that in just yet.  

```python
def on_received_string(receivedString):
    global sender, protocol_state, opponent_rps
    if receivedString.includes("request"):
        # same as before...
    elif receivedString.includes("response"):
        # same as before...
    elif receivedString.includes("acknowledge"):
        # same as before...
    elif receivedString.includes("play"):
        if protocol_state >= 2 and parse_float(receivedString.split(" ")[1]) == my_id and parse_float(receivedString.split(" ")[2]) == sender:
            opponent_rps = parse_float(receivedString.split(" ")[3])
            if protocol_state == 3:
                check_play()
            else:
                protocol_state = 4       
```

Similarly, we'll update the A+B button press to see if we have already received our partner's guess:

```python
def on_button_pressed_ab():
    global protocol_state, sender, my_id, rps
    if protocol_state >= 2:
        radio.send_string("play" + " " + convert_to_text(sender) + " " + convert_to_text(my_id) + " " + convert_to_text(rps))
        if protocol_state == 4:
            check_play()
        else:
            protocol_state = 3
input.on_button_pressed(Button.AB, on_button_pressed_ab)
```

##### Determining the Winner
We can create a function called `check_play()` to compare the `rps` variable with `opponent_rps`.  Here are the rules:
1. Paper (1) covers Rock (0)
2. Rock (0) crushes Scissors (2)
3. Scissors (2) cuts Paper (1)
4. If they're the same, it's a tie

Here's the function.  We'll display an icon on the screen depending on the result.  We'll set result to 1 for a win, 0 for a loss, and 2 for a tie:

```python
def check_play():
    global result
    basic.show_icon(IconNames.SURPRISED)
    if rps == opponent_rps:
        result = 2
    elif rps == 0 and opponent_rps == 2:
        result = 1
    elif rps == 1 and opponent_rps == 0:
        result = 1
    elif rps == 2 and opponent_rps == 1:
        result = 1
    else:
        result = 0
    if result == 1:
        basic.show_icon(IconNames.HAPPY)
    elif result == 2:
        basic.show_icon(IconNames.STICK_FIGURE)
    else:
        basic.show_icon(IconNames.SAD)
```

#### Finishing Touches

##### Determining the End of the Game
I created one final state (state number 5) to mean finished.  When should this be set?  Basically, whenever you call `check_play()` (which can happen in one of two places).  Or, you can set the state to 5 right inside `check_play()`.  Then, add an clause to all of your `if` statements to make sure that the state value is not 5.  This way, you'll ignore all messages if state is 5.  You can reset the micro:bit to start again.

##### Setting the ID Number
In the very beginning, we made everyone's ID 0!  We had to pick something, and without asking everyone to edit their own program, the ID has to be the same for everyone at first.  When you press the A button, let's increase the ID number by 1, so that everyone can pick their own ID number.  We'll show the ID on the screen when this happens so you can be sure.

```python
def on_button_pressed_a():
    global my_id
    my_id += 1
    basic.show_number(my_id)
input.on_button_pressed(Button.A, on_button_pressed_a)
```

##### Missed Connections
What happens if you start to connect with someone but then walk away, or they connect with another partner because of a tie during the three-way-handshake?  Maybe we could reset after a few seconds and try again, by setting our state back to 0 to show that we're looking for a partner again.  In the forever loop, we'll increase a new variable called `age` by 1 every time.  When this variable becomes some large number like 10, we'll reset our state to 0.  I'm not going to reset our ID number, so that we don't have to set that up again, but we'll reset everything else.

```python
def reset():
    global protocol_state, sender, rps, opponent_rps, result, age
    protocol_state = 0
    sender = 0
    rps = 0
    opponent_rps = 0
    result = 0
    age = 0
    basic.show_number(my_id)
```

Here's the new `forever` loop that checks the `age` each time, if the protocol state hasn't reached 2 (meaning you're paired with a partner).  What would happen if the `age` check didn't check the state, and you were already paired with someone?

```python
def on_forever():
    global age
    if protocol_state == 0:
        radio.send_string("request" + " " + convert_to_text(my_id))
    basic.pause(1000)
    if protocol_state <= 1:
        age = age + 1
        if age > 10:
            reset()
```            
        
### The Finished Project

Here's my finished product, available at [https://www.billmongan.com/rock-paper-scissors-protocol/](https://www.billmongan.com/rock-paper-scissors-protocol/):

<p align="center">
<img style="max-width:100%;" alt="The finished hide and seek project in the Microbit Makecode" src="https://github.com/billjr99/rock-paper-scissors-protocol/raw/master/.github/makecode/blocks.png">
</p>

Here is the code in Python:
<p align="center">
<script src="https://gist.github.com/BillJr99/2e8019816a7b14746a600988fce855f1.js"></script>