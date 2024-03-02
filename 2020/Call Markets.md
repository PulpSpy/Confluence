# Closing Cross for Call Markets

NYSE and NASDAQ operate a call market for closing prices all day (not just in the last few minutes). Obviously trades happening early in the day would tend to use the real-time orderbook for more immediate execution, however some regulated entities might be forced to operate only in the call market. They might submit their bids once a trade decision has been made (e.g., early in the day).

The timeline for the closing of **NASDAQ** is:

| Time  | Activity                                                     |
| ----- | ------------------------------------------------------------ |
| 4h00  | Call market opens                                            |
| 15h50 | New market orders cannot be submitted; limit orders accepted |
| 15h50 | Auction info (provisional close and trade imbalance?) disseminated every 5 seconds |
| 15h55 | Orders cannot be cancelled or modified                       |
| 15h58 | New limit orders cannot be submitted                         |
|       |                                                              |

The timeline for the **NYSE** is similar except that NYSE uses real human beings on the trading floor called "floor traders." Once markets start to close, new orders, modifications, and cancellations can only be made through a floor trader, not electronically. The end result is worst pricing: [Paper](https://ssrn.com/abstract=3600230)

Electronic trading in the US started very slowly, but poeple kept at it and eventually it became dominant. This is a good lesson for technology in general, and particularly for blockchain technology which has seen slow uptake in real markets.

| Date | Activity                                                     |
| ---- | ------------------------------------------------------------ |
| 1969 | Instinet Launches                                            |
| 1971 | NASDAQ                                                       |
| 1987 | 189M shares/day on NYSE, <10M shares/day on Instinet         |
| 1994 | TerraNova launches (Archipelago Exchange or ArcaEx)          |
| 1997 | Island ECN launches                                          |
| 2002 | Inet launches (merger of Instinet acquires Island)           |
| 2005 | NASDAQ acquires Inet (some of Instinet is sold off and continues today) |
| 2006 | NYSE acquires ArcaEx                                         |
|      |                                                              |
|      |                                                              |
|      |                                                              |
|      |                                                              |
|      |                                                              |
|      |                                                              |