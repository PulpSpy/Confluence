# Monty Hall

###### Grok the monty hall problem with one stupid trick

###### Jeremy Clark

Steven Pinker's book *Rationality* is a good synthesis of a bunch of scientific non-fiction over the years. I am definitely not the target audience for Pinker's book. I think nearly every topic he covers was already detailed in one or more of the books I've read over the past 20 years (including off the top of my head: *The Undoing Project; The Drunkard's Walk; Nudge; Predictable Irrational; Fooled by Randomness; Thinking, Fast and Slow; The Signal and the Noise; Freakonomics; Superforecasting; Innumeracy; (Mis)Behavior of Markets; etc., etc.*). 

Given all this, *Rationality* is still an enjoyable read! I think it is nearly a complete stand-in for 20+ other books, as many of those have only a few key ideas. I did learn a few new things as well! One topic that comes up a lot in this literature is the Monty Hall problem. From Wikipedia, it is described as:

> Suppose you're on a game show, and you're given the choice of three doors: Behind one door is a car; behind the others, goats. You pick a door, say No. 1, and the host, who knows what's behind the doors, opens another door, say No. 3, which has a goat. He then says to you, "Do you want to pick door No. 2?" Is it to your advantage to switch your choice?

The paradox is that switching does help. The probability of winning goes from $\frac{1}{3}$ to $\frac{2}{3}$. 

Pinker builds intuition for this paradox in a common way: think about 100 doors where 98 are opened. However, almost in passing and perhaps unintentionally, he suggests a way of thinking about the problem that I have *never* encountered before! The trick is to stop thinking about the choices from the **contestant's** perspective, which leads to confusing probabilities. Instead think through the problem from the **host's** perspective. The host also has decisions to make. What are they? 

- The contestant picks a door. 
- You (the host) now have to pick another door to open.
  - Do you have a choice or not in which door you choose to open? 

  - It depends...

- Contestant guessed wrong (**2/3 probability**):
  - You (the host) have two doors to choose from
  - But you have no choice, you have to open the only door without the prize!
  - The contestant will **win** **if they switch**
  - The contestant will lose if they don't switch
- Contestant guessed right (**1/3 probability**):
  - You (the host) have two doors to choose from
  - Since both are wrong, you can choose randomly which one to open
  - The contestant will lose if they switch
  - The contestant will **win if they don't switch**
- Should the contestant switch? 
  - 2/3 of the time they will win if they switch
  - 1/3 of the time they will lose if they switch
  - Therefore they should switch