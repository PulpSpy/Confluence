# Monty Hall

###### Understand the Monty Hall problem with one stupid trick

###### Jeremy Clark

Steven Pinker's new book *Rationality* is a good synthesis of a bunch of scientific non-fiction over the years. I am definitely not the target audience for the book; I think nearly every topic has been covered in other books I've read (including off the top of my head): *The Undoing Project; The Drunkard's Walk; Nudge; Predictable Irrational; Fooled by Randomness; Thinking, Fast and Slow; The Signal and the Noise; Freakonomics; Superforecasting; Innumeracy; (Mis)Behavior of Markets; etc.* Given all this, it was still an enjoyable read, I would recommend it to replace reading 20+ other books, and I did learn a few new things even! 

One topic that comes up a lot in this literature is the Monte Hall problem. From Wikipedia:

> Suppose you're on a game show, and you're given the choice of three doors: Behind one door is a car; behind the others, goats. You pick a door, say No. 1, and the host, who knows what's behind the doors, opens another door, say No. 3, which has a goat. He then says to you, "Do you want to pick door No. 2?" Is it to your advantage to switch your choice?

The paradox is that switching does help. The probability of winning goes from 1/3 to 2/3. 

Pinker gives a standard description and builds intuition for it in a standard way (e.g., think about 100 doors instead of 3). However, almost in passing and perhaps unintentionally, he suggests a way of thinking about it that I have never read before! Beyond being novel (to me anyways), a simple trick makes the problem really easy to reason through. This is now my favorite way to think of it (even better than the 100 doors variant)

The trick is to stop thinking about the choices from the game show **contestant's** perspective, which is leads to confusing probabilities. The trick is to think about the choices the game show **host** needs to make. From the host's perspective:

- Contestant picks a door. 
- You (the host) now have to pick another door to open.
- Do you have a choice or not in which door you choose to open? It depends...

- Contestant guessed wrong (2/3 probability)
  - You (the host) have two doors to choose from
  - But you have no choice, you have to open the only door without the prize
  - The contestant will then **win** **if they switch**
  - The contestant will lose if they don't switch
- Contestant guessed right (1/3 probability)
  - You (the host) have two doors to choose from
  - Since both are wrong, you can choose randomly which one to open
  - The contestant will then **lose** **if they switch**
  - The contestant will win if they don't switch
- Should the contestant switch? 
  - 2/3 of the time they will win if they switch
  - 1/3 of the time they will lose if they switch
  - Therefore they should switch