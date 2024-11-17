# Loss vs Rebalancing (LVR)

This will be a gentle introduction to the concept of loss vs rebalancing (LVR). If you want to figure out whether adding liquidity to an automatic market maker (AMM) will make you a profit or not, LVR is a key part of the answer. This article is not a summary of the research on LVR, it is instead an extended on-ramp before reading the research, for those who have found the research jumps into the deep end of AMMs too quickly.

Paper:

* "[Automated market making and loss-versus-rebalancing](https://arxiv.org/abs/2208.06046)" by Jason Milionis, Ciamac C. Moallemi, Tim Roughgarden, and Anthony Lee Zhang.

Videos from authors:

* Tim Roughgarden (3 min): https://www.youtube.com/watch?v=O7WoMeC0zow
* Tim Roughgarden (26 min): https://www.youtube.com/watch?v=q5vyJJb-Uyw
* Anthony Zhang (50 min): https://www.youtube.com/watch?v=XmHBiFApDXA
* Jason Milionis (60 min): https://www.youtube.com/watch?v=Xz2PfxlKOZM
* Ciamac Moallemi (65 min) - https://www.youtube.com/watch?v=Y_3IzdJdUWA



## Automatic market makers (AMMs)

Automatic market makers (AMMs) like Uniswap allow users to trade tokens. AMMs work differently than traditional markets that use a continuous time orderbook (also known as a limit order book or LOB) for real-time trade. Often forgotten, most stock markets also run two call markets (or batch auctions), one a few hours before the market opens and the other one over the trading day, to compute the opening and closing price respectively. Beyond AMMs, LOBs, and call markets, there are not many other types of market (maybe one more prominent type is a crossing network, used by many dark markets, which operates like a call market but pulls its clearing price from an external lit market, like a LOB that trades the same asset—AMMs could be similarly designed). 

AMMs, LOBs, and call markets have different properties. In my opinion, you would never choose to use an AMM unless if a LOB or call market was unavailable (researchers have to stretch far to find obscure places where an AMM outperforms a LOB). One place LOBs and call markets are unavailable is on Ethereum because they both require linear time operations, which is too expensive to execute solely on-chain. By "expensive," I don't just mean more than you want to pay---I mean they can break through the maximum gas limit. AMMs are a "dumbed down" version where every operation is constant time.

An AMM is like a vending machine. It is loaded with two kinds of tokens, say red and black. If a trader wants to take black tokens out, they have to put red tokens in. The smaller the pile of black tokens in the machine gets, the more red tokens are required to continue taking black tokens. You can never take all the black tokens out because the machine will eventually require an infinite number of red tokens.

How do the tokens get there in the first place? Anyone can be a liquidity provider (LP) and put both red and black tokens into the machine (matching the ratio of the piles in the machine). LPs can also take their tokens out any time they want, however because the piles have changed sizes, they will not necessarily get the same ratio of tokens out. The rest of this article concentrates on LPs: what they stand to gain and how they might lose.

## Informed traders

Consider this passage from Larry Harris (Trading & Exchanges). It concerns "dealers" which are always ready to buy or sell an asset. They are the human version of the same role that LPs play in AMMs. 

> Traders who trade only to accommodate other traders risk trading with, and losing to, well-informed speculators. Speculators are traders who trade to profit from information they have about future prices. Well-informed speculators can predict futures prices better than other traders can. They then choose to buy or sell based upon which side they expect will be profitable. Dealers lose to well-informed speculators because they end up being on the wrong side of the trade. Prices tend to move against their positions before they can trade out of them. All traders try to avoid trading with well-informed speculators.
>
> Dealers recover their losses to informed speculators by widening the spread between the bid and ask prices at which they will buy and sell. Uninformed traders therefore pay more for their trades when dealers lose a lot to informed traders. In effect, uninformed traders lose to well-informed traders through the intermediation of dealers.
>
> Traders who can estimate fundamental values cause prices to reflect their value estimates. They buy when price is below their value estimates and sell when price is above. Their buying pushes prices up, and their selling pulls prices down. They do not trade if they believe that prices reflect values. Well-informed traders make prices informative.

Like dealers, LPs are not informed about prices. They (or the AMM on their behalf) trade to accommodate other traders. An individual LP might be informed about prices but the AMM does not give them any way of acting on it (other than timing when to remove liquidity), at least in the basic form of an AMMs (next generation AMMs do partially address this with something called concentrated liquidity). Once an LP loads the AMM with tokens, they sit back and watch informed and uninformed traders upset the ratio of tokens they provided. This would be a very bad deal for the LP except they make fees on every trade. 

(Note the quote above describes dealers making money from widening spreads. If an AMM will buy 1 unit at \$100 with a 1% fee, or sell at \$100 with a 1% fee, they are effectively selling at \$101 and buying at \$99. The \$2 difference is the "spread." As fees increase, the "spread" increases as well. Dealers are active in adjusting their prices based on market conditions. LPs are passive, they pick a fee, deposit their tokens, and let traders use the AMM without further intervention.)

 An LP hopes that the fees they charge to all traders outweigh what they lose to informed traders. So what exactly do they lose and how is it quantified? 

## Does "loss" always mean the same thing?

Alice is dismayed to find her computer infected with ransomeware. After exhausting every technical measure to restore it, she gives in and pays 0.02 BTC. After the transaction is confirmed and her computer is restored, she has loss 0.02 BTC and her attacker has gained 0.02 BTC. This is the simplest type of loss, it has a clear winner and a clear loser. We will call it a **type 1 loss**.

Later, after packing her things for a move, Alice is delighted to find an old harddrive with 50 BTC on it that she mined in 2009. She does a quick mental calculation and decides it cost her around \$100 in hydro to pay for all the mining she did. Mallory is helping Alice pack and cuts her a deal: Mallory will give Alice \$1000 for the 50 BTC (despite 50 BTC being worth \$4M+ USD).

Did Alice lose money? In one way of looking at it, she received \$1000 for something that cost her \$100 so she made a 10x return. Another way of looking at it, she sold a \$4M+ asset for \$1000 meaning she lost \$4M. The first way of looking at it is her **type 1 loss** (which is a gain in this case), while the second way of looking at we will call a **type 2 loss**. Type 2 is harder to see because while Mallory is a clear winner with a gain of \$4M, it is less clear where the \$4M came from because it was not in Alice's pocket. It does come at Alice's expense though, the \$4M was a "unrealized gain" that belonged to Alice. Reading this story, I would guess you think Alice got a bad deal, which means implicitly you are accounting for losses per type 2 and not just type 1.

There is a third kind of loss which is actually not a loss at all, but enough people think it might be that I'll call it a **type 3 loss**. On 22 May 2010, Bitcoin Talk user laszio paid 10000 BTC for 2 large pizzas. To this day, journalists frame this story as squandering BTC on a *million dollar pizza*. This is not a type 2 loss because the loss was not realized at the time of the transaction. Laszio missed out on a future opportunity (hodl the 10K BTC which is now worth \$100M+ USD) but knowing the future is not possible. In an alternate universe, BTC might have collapsed in value and laszio would look good for at least getting something for his worthless digital money.

In summary,

Type 1: Alice loses an asset of value that she holds

Type 2: Alice has an unrealized gain and she loses the ability to capitalize on it

Type 3: Alice loses a future opportunity that results in a gain

How does this relate to AMMs? If Alice is a liquidity provider for an AMM and she walks away with assets worth less than what she put in, that is a type 1 loss and it has been identified and studied under the name **impermanent loss (IL)**. Whether Alice has any type 2 loses and how to quantify them was not studied until the **loss versus rebalancing (LVR)** paper. The contribution of LVR paper is pointing out that Alice indeed has type 2 losses and it provides a way to quantify them. Alice may also have type 3 "loses" by choosing to be an LP instead of some other investment opportunity (e.g., she would make more money if she sold all her tokens for oil futures). Type 3 is not studied because we do not assume Alice knows the future. 

## Losses as comparisons

The common denominator of all three kinds of losses is a comparison between what Alice actually does and what Alice might have done instead.

* In the Type 1 example, Alice might never had been infected with ransomware. We calculate her loss by comparing her assets in both worlds.
* In the Type 2 example, Alice might have known the value of BTC and received \$4M USD instead of what she received in the real world. Again, her lose is the difference between what she receives in both worlds. 
* In the Type 3 example, laszio might have held onto the BTC instead of buying the pizza.

Why is Type 3 not a real loss? To be a "real" loss, we will consider at least two conditions as necessary:

1) "Your opportunity, your loss"
2) "No crystal ball"

The first condition means the loss must be attributable to Alice (or laszio). In the case of found BTC, the unrealized gain belongs to Alice and she loses it. In the case of the million dollar pizza, it is not specific to laszio. If you ever bought a pizza for $20 in 2010, then you too bought a million dollar pizza and have the same type 3 loss as laszio. Why? Because you could have bought \$20 worth of BTC instead of \$20 worth of pizza, and you would have a hundred million dollars. The opportunity cost to buy and hold BTC is not specific to laszio.

The second condition means that the comparison needs to be made between two versions of the present universe, not a comparison between the present and the future. Alice could have learned her found BTC was worth \$40M, it does not require a crystal ball or predicting the future, just being aware of current prices. Laszio is equally rich in the universe where he holds the BTC and where he is eating pizza *at the time of the transaction*. The difference (loss) does not incur until the future but laszio does not have a crystal ball *at the time of the transaction*. So it is not a loss. It might be some other bad thing (poor speculation) but not a loss.

## Impermanent Loss (IL) 

We will now attempt to quantify when Alice might lose money as an LP. We will look a few different ways of measuring this, ultimately considering LVR. We will consider a worked example. In it, Alice decides to be an LP for an AMM for ETH and USDC. We will assume that the price of ETH changes over time. The price change will first happen outside the AMM (on a centralized exchange or CEX), leaving the AMM with a stale price. We say the AMM is unbalanced because its ratio of tokens does not reflect the correct price. An unbalanced AMM is "free money" for the first person to notice it is unbalanced and trades in a way that balances in it. It does not matter if the AMM becomes unbalanced because ETH goes up in price or if it becomes unbalanced because ETH goes down in price.

The key points:

* Price updates will happen in two steps: first on the CEX and then on the AMM.
* After the price updates on the CEX and before the price updates on the AMM, the AMM is unbalanced.
* An unbalanced AMM has free money for the taking. Since the money "belongs" to Alice (she owns the tokens in the AMM as the LP and can withdrawal at any time), taking this free money is taking money from Alice. So it is Alice's loss.
* As long as prices move, Alice will lose money being an LP. She will offset these loses by charging fees to trade with her AMM but for now, we will ignore all fees (both at the AMM and later at the CEX as well). Once we grasp LVT then we will consider fees.

At the time of writing, 1 ETH is trading for \$3270 USD. Consider an AMM that reflects this price (substituting USD with a stablecoin like USDC). Assume Alice starts with \$1000 in USDC and \$1000 in ETH (~0.31 ETH). If we 'mark to market' her assets, Alice has the cash equivalent of \$2000. Assume Alice places her tokens into an AMM as an LP. (I'll skip all the math but I am assuming she is the first and only LP in the AMM, fees are 0.3%, and the AMM uses a constant product mark maker $k$ to compute prices.)

Below is a trace of events that happen. At event (1), price of ETH goes to \$4000 in the external CEX market (and not in the AMM yet), which changes the value of Alice's holdings. Because the AMM is unbalanced (the price does not reflect the market price), a trader with USDC can remove ETH for less than it is worth, which is considered arbitrage because it is profit without risk for this trader. As ETH is removed in step (2), the prices in the AMM changes and the trader will stop removing ETH when the price in the AMM reflects the external price. An unbalanced AMM always represents "free" money for the first person to trade with it. 

Next the price drops to \$3000 externally (step 3) and again the AMM is unbalanced. Because the price on the AMM is higher than externally (rather than lower, as before), the arbitrageur with ETH will remove USDC until the AMM is balanced against the external price (step 4).

Finally the price returns to its original price of \$3270 (step 5) and the value is extracted from the AMM (step 6). 

|      | Event              | ETH/USD (External) | ETH/USD (AMM) | Pool Value (AMM) | Arbitrage |
| ---- | ------------------ | ------------------ | ------------- | ---------------- | --------- |
| 0    | Init. AMM          | \$3270             | \$3270        | \$2000.00        |           |
| 1    | Price Update (CEX) | \$4000             |               | \$2223.24        |           |
| 2    | Arbitrage (AMM)    |                    | \$4000        | \$2212.01        | \$11.23   |
| 3    | Price Update (CEX) | \$3000             |               | \$1935.50        |           |
| 4    | Arbitrage (AMM)    |                    | \$3000        | \$1915.65        | \$19.85   |
| 5    | Price Update (CEX) | \$3270             |               | \$2001.86        |           |
| 6    | Arbitrage (AMM)    |                    | \$3270        | \$2000.00        | \$1.86    |

Now let's think of a few ways we could define what Alice loses by being an LP. 

One way is to look at the pool value at the end (\$2000) and compare it to the pool value at the start (\$2000). In this case, the difference is zero. The problem with this measure is that combines two things. The first is the external prices. The second is how much it costs to actually be an LP. Consider if Alice removed her liquidity after step 2 above. Her gain would \$212.01. Was that because being an LP makes money? Or was it just because ETH went up in price? We want to isolate Alice's gains/losses from being an LP.

To do this, we will compare Alice (as an LP) with some other things Alice can do instead of being an LP. One strategy Alice could do is just hold (or "hodl" if you speak crypto) onto the $1000 USDC and 0.31 ETH tokens without becoming an LP. The difference between the two Alices is below:

|      | Event              | ETH/USD (CEX) | ETH/USD (AMM) | Alice (LP) | Alice (Hodl) | IL      |
| ---- | ------------------ | ------------- | ------------- | ---------- | ------------ | ------- |
| 0    | Init. AMM          | \$3270        | \$3270        | \$2000.00  | \$2000.00    |         |
| 1    | Price Update (CEX) | \$4000        |               | \$2223.24  | \$2223.24    |         |
| 2    | Arbitrage (AMM)    |               | \$4000        | \$2212.01  | \$2223.24    | \$11.23 |
| 3    | Price Update (CEX) | \$3000        |               | \$1935.50  | \$1917.43    |         |
| 4    | Arbitrage (AMM)    |               | \$3000        | \$1915.65  | \$1917.43    | \$1.78  |
| 5    | Price Update (CEX) | \$3270        |               | \$2001.86  | \$2000.00    |         |
| 6    | Arbitrage (AMM)    |               | \$3270        | \$2000.00  | \$2000.00    | \$0.00  |

Note the column marked Alice (LP) is the same as Pool Value (AMM) from the first chart. A second way of measuring gains/loss is as follows:

* Impermanent Loss (IL): the difference between Alice (LP) after arbitrage and Alice (Hodl).

A few notes about this measure. The first is that it can never be a gain. Alice (LP) will always have less than or equal to Alice (Hodl) and the only time it is equal is when the end price matches the starting price. The further the price drifts from the starting price, the greater the loss. If the price drifts back to the starting price, the loss goes to zero.

## Loss vs Rebalancing (LVR)

So far, we are comparing Alice (LP) to the best strategy that Alice can have, short of having a crystal ball. If Alice (hodl) is the best strategy, then IL is the right notion of loss. But if there is a better strategy, then we need to measure Alice (LP) values against it. The LVR paper shows there is a better strategy, which it calls rebalancing. 

Before describing the rebalancing strategy, let's convince ourselves that Alice might be able to do better than hodl, at least in theory. Examine the chart above again, this time noticing that Alice (LP) after a price update (on CEX) and before arbitrage (on AMM) does outperform Alice (Hodl). Is there anything Alice can do to capture that money? One thing Alice could do is rush to the AMM after every price update, be the first trader that arbitrages the AMM, and keeping the arbitrage amount (from the first chart) in addition to her holdings as Alice (LP). We show this as Alice (Rush).

|      | Event              | ETH/USD (CEX) | ETH/USD (AMM) | Alice (LP) | Arb     | Arb (Accum) | Alice (Rush) |
| ---- | ------------------ | ------------- | ------------- | ---------- | ------- | ----------- | ------------ |
| 0    | Init. AMM          | \$3270        | \$3270        | \$2000.00  |         |             | \$2000.00    |
| 1    | Price Update (CEX) | \$4000        |               | \$2223.24  |         |             | \$2223.24    |
| 2    | Arbitrage (AMM)    |               | \$4000        | \$2212.01  | \$11.23 | \$11.23     | \$2223.24    |
| 3    | Price Update (CEX) | \$3000        |               | \$1935.50  |         |             | \$1946.74    |
| 4    | Arbitrage (AMM)    |               | \$3000        | \$1915.65  | \$19.85 | \$31.09     | \$1946.74    |
| 5    | Price Update (CEX) | \$3270        |               | \$2001.86  |         |             | \$2032.95    |
| 6    | Arbitrage (AMM)    |               | \$3270        | \$2000.00  | \$1.86  | \$32.95     | \$2032.95    |

The problem with Alice (Rush) is that the assumption Alice can outpace everyone in the market is not reliable. It is the same flavour as assuming Alice has a crystal ball—we cannot assume Alice has special powers.

What if Alice is fast but not necessarily the fastest? Whether or not Alice can get to the AMM first, assume she can at least squeeze in an action between each price update on the CEX. In this case, she can start the same as Alice (Hodl) but she can adjust her holdings based on every price update (on the CEX). Here are a few ideas:

* Alice could trade ETH for USDC (or vice-versa) so that her piles of each always have the same value
* Alice could see what trade will happen on the AMM (regardless of whether it has already happened or not, she can compute what it will be if it hasn't) and do the same trade on her holdings using the CEX

If you run these two strategies, you will notice the second one does better. It is called rebalancing and is shown as Alice (Rebal) below. We put it alongside all the things we have talked about for one final chart.

|      | Event              | ETH/USD (CEX) | ETH/USD (AMM) | Alice (LP) | Alice (Hodl) | IL      | Alice (Rush) | Arb (Acc) | Alice (Rebal) | LVR     |
| ---- | ------------------ | ------------- | ------------- | ---------- | ------------ | ------- | ------------ | --------- | ------------- | ------- |
| 0    | Init. AMM          | \$3270        | \$3270        | \$2000.00  | \$2000.00    |         | \$2000.00    |           | \$2000.00     |         |
| 1    | Price Update (CEX) | \$4000        |               | \$2223.24  | \$2223.24    |         | \$2223.24    |           | \$2223.24     |         |
| 2    | Arbitrage (AMM)    |               | \$4000        | \$2212.01  | \$2223.24    | \$11.23 | \$2223.24    | \$11.23   | \$2223.24     | \$11.23 |
| 3    | Price Update (CEX) | \$3000        |               | \$1935.50  | \$1917.43    |         | \$1946.74    |           | \$1946.74     |         |
| 4    | Arbitrage (AMM)    |               | \$3000        | \$1915.65  | \$1917.43    | \$1.78  | \$1946.74    | \$31.09   | \$1946.74     | \$31.09 |
| 5    | Price Update (CEX) | \$3270        |               | \$2001.86  | \$2000.00    |         | \$2032.95    |           | \$2032.95     |         |
| 6    | Arbitrage (AMM)    |               | \$3270        | \$2000.00  | \$2000.00    | \$0.00  | \$2032.95    | \$32.95   | \$2032.95     | \$32.95 |

The amount Alice loses is: LVR = Alice (Rebal) - Alice (LP). The other columns are for making comparison. The key points about LVR:

* The Alice/Rebalancing strategy outperforms Alice/LP strategy.
* The Alice/Rebalancing strategy matches the Alice/Arbitrage strategy.
* The Alice/Rebalancing strategy outperforms all other strategies without giving Alice "special powers" like predicting the future, being faster than all arbitrageurs, or access to external capital/leverage.

## Fees

If Alice is considering being an LP, LVR is only half of the story. It showcases how much money she will lose being an LP relative to what else she could do her tokens. Of course, if there is only loses, no one will be an LP. Alice will also earn fees on every transaction, which are typically a percentage of the trade value. So Alice's decision needs to consider her profit and loss (P&L) which is the revenue earned from fees minus the LVR.

LVR and transaction fees are both proportional to the amount of the price change, however large price changes accentuate LVR over fees. An AMM where prices do not change much but there is still trading activity (e.g., an AMM with two kinds of stablecoins)  will be overpowered by fees. An AMM where prices change a lot (e.g., a prediction market where losing shares can go to \$0 quickly) be overpowered by LVR. The amount prices change is called volatility and the LVR paper has some mathematical relationships between LVR and volatility.

Thus one way to help LPs in AMMs is to make sure fees are appropriate for the level of volatility in the market. How do we determine volatility?  There are two ways, neither perfect. One is to look historically, say over the lagging 6 months, and compute it. But this does not mean the volatility over the next 6 months will match. The second way is to try and get a consensus from the market about what traders think the volatility will be. Traders will not tell you explicitly, but they often trade instruments (like futures and options) that have implicit assumptions about volatility built into their price. Looking at the prices, you can back out what the implied volatility is being guessed by traders. 

Another important aspect of volatility is how the mathematics underlying the equations model the volatility. Some mathematical models, such as Black-Scholes, prioritize mathematical cleanliness (as clean as stochastic calculus can be) over realism and put unrealistic bounds on volatility. More sophisticated (but mathematically complex) models like Jump-Diffusion or GARCH correct for this. 

So what can AMMs learn from the study of LVR?

* It is hard to be a passive LP (just provide your tokens and walk away) and expect to make money.
* With a volatility estimate, fees can be set to dominate LVR, but the estimate might be wrong. 
* LPs that are willing to be active can create a kind of insurance policy against LVR by operating a rebalancing strategy on a CEX in parallel to being an LP. However this strategy operates as a "short" strategy which requires fancier offerings from CEXes that are not always available or of the exact nature needed, and when available, are not always liquid enough to have low fees. This could improve as the crypto market matures.
* AMMs that are willing to be active can operate like a "crossing network" where the AMM matches buyers/sellers but does not set prices. The prices are fed into the AMM from external sources. 

