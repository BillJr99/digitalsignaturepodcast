---
layout: activity
title: "Pseudo Random Number Generation with the Microbit"
permalink: /activities/prng

gradelevel: elementary
category: microbit
subject: statistics

gitrepo: https://github.com/billjr99/random-number-generator

summary: "In this activity, we will use the Micro:bit to create a sequence of pseudo-random numbers using a formula."

thumbnail: "https://upload.wikimedia.org/wikipedia/commons/3/36/Two_red_dice_01.svg"
---

How do you think the Micro:bit generates a random number for the dice?  It is difficult to create a random number with a computer, because computers &quot;compute&quot; values with formulas.  These formulas, called [pseudo-random number generators](https://www.geeksforgeeks.org/pseudo-random-number-generator-prng/), create sequences in which the current value of the function is the input to the next calculation.  

### Getting Started: Generating Random Numbers

What makes a random number <i>random</i>?  Brainstorm a few ideas with the person next to you, and share them out with the rest of the class.

Generally, random numbers should be difficult to guess, and it is preferable that they be uniform.  That is, over time, these random number sequences should produce a similar number of values.  Imagine rolling a dice that never rolled a 5!  You would quickly figure this out; every dice value should occur sometimes, and with equal probability.  That is a uniform distribution of values.

Come up with 10 random numbers between 1 and 6 (like rolling a dice) and share them with the person next to you.  Don't write them down, just say them out loud.  Your partner can keep track of how many 1's, 2's, 3's, 4's, 5's and 6's you say.  When you're done, switch roles and do this again.  How many of each value did you come up with?  Are they roughly uniform?

#### Generating Random Numbers with a Computer

One formula for generating random numbers is the [Linear Congruential Generator](https://en.wikipedia.org/wiki/Linear_congruential_generator).  It uses this formula:

<span>\\(X = (aX + b) (mod \; c)\\)</span> 

Where:

* <strong>X</strong> is the current random number value
* <strong>a</strong> is a multiplier that we multiply the random number by
* <strong>b</strong> is a value that we add to the random number
* <strong>c</strong> is the &quot;&modulus&quot; that we take the remainder of the random number

The new random number <strong>X</strong> depends on the prior value: you can see this because <strong>X</strong> is on the left side of the equation and the right side of the equation.  Make up some values for <strong>a</strong>, <strong>b</strong>, and <strong>c</strong>, and generate some random numbers on paper using a calculator.  You'll need a starting random number of <strong>X</strong>: pick any number you like!  This is called a <strong>seed</strong> value, and it determines all the random numbers that this function will generate.

### Random Number Generation using the Micro:bit

We will use the Micro:bit to implement this formula and generate random numbers.  To do this, we will need an <strong>on start</strong> block and an <strong>on button A pressed</strong> block on [Makecode](https://makecode.microbit.org/).

For beginners, it is a good idea to provide the <strong>on start</strong> block as starter code using [Makecode Classroom](https://classroom.microbit.org/).

#### On Start

Within the <strong>on start</strong> block, let's set up the variables from the formula.  There were 4 of them: a <strong>modulus</strong>, a <strong>multiplier</strong>, an <strong>adder</strong>, and a <strong>X</strong> random number (which I will call <strong>current_random</strong> since I prefer descriptive variable names!).  Set these to any values you like.  I set them as follows, but the numbers themselves are completely made up:

```python
current_random = 371
modulus = 5891
multiplier = 1763
adder = 2565
```

Now we're ready to use these variables in our formula to start generating random numbers.  Let's add one more item to <strong>on start</strong> to actually see the number.  Add a <strong>show number</strong> step that displays the <strong>current_random</strong> variable.

#### On Button Pressed
When the <strong>A</strong> button is pressed, we'll run the formula to generate a new random number.  There are a few ways to do this, but generally, we'll need to multiply <strong>current_random</strong> by the <strong>multiplier</strong>, then add that to <strong>adder</strong>, and, finally, comptue the remainder after dividing by <strong>modulus</strong>.  The <strong>modulus</strong> keeps the values from getting too big, since we are always adding and multiplying to make the value bigger.  The <strong>modulus</strong> keeps our random numbers between 0 and the value of <strong>modulus</strong>.

Of course, you'll want to see the value on the LED output, so add that <strong>show number</strong> step that you saw before.

#### Optional: Random Numbers from 0 to 9
It's easier to see single digit numbers on the Microbit, but single digit values are hard to use to generate random values using this formula.  Rather than change the random number itself, we'll just show the last digit of the random number on the display.  Change both of the <strong>show number</strong> steps to show the remainder after dividing <strong>current_random</strong> by <strong>10</strong>.  I created one extra variable called <strong>max_value</strong> to store this value <strong>10</strong>, so that I could change it easily later (and to help me remember what it was for!).

### Finished Product
Here's my finished product, available at [https://www.billmongan.com/random-number-generator/](https://www.billmongan.com/random-number-generator/):

<p align="center">
<img alt="The finished hide and seek project in the Microbit Makecode" src="https://github.com/billjr99/random-number-generator/raw/master/.github/makecode/blocks.png">
</p>

### Extension: Playing with the Seed Values

How uniform were your random numbers?  Hit the <strong>A</strong> button a few times to generate some pseudo-random numbers, and count how many of each number you see.  How uniform are they?  

Try changing the <strong>modulus</strong>, <strong>multiplier</strong>, and <strong>adder</strong>, values to ones that are more uniform, or ones that are less uniform.  What do you notice about them?  For example, advanced students might try larger numbers to obtain more uniformity, while others might choose odd or prime numbers.  