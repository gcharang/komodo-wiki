
#### Q. What are smartaddresses?
A. Smart addresses are a set of local coin addresses that are derived from the main passphrase. The same passphrase is used for all coins and different types of smart addresses can be logged into directly by adding a prefix for each specific type of smartaddress.

#### Q. What does a smartaddress do?
A. Depending on the type of smartaddress it will either start a jumblr sequence or just do a DEX swap. For KMD the jumblr process stays within KMD, for all other coins, the jumblr process converts to KMD first and then feeds into the KMD jumblr. The “deposit” type is used for this, similar to depositing funds at an exchange deposit address that is customized for you, there is a “deposit” type of smartaddress for each active coin. You just send coins to that address and it automatically does a DEX to KMD and from there it is jumble’ed. Currently all jumble’ed funds end up as jumblr’ed BTC as that is the mainstream case. 

#### Q. Is it possible to disable converting jumblr into BTC.
A. Yes, the runsilent API can be used to prevent the last of three KMD jumblr transactions, the Z -> T transaction. This has the benefit of maximizing privacy over time and prevents the conversion into BTC.

#### Q. What does the “jumblr” type smartaddress do?
A. That is a special type that designates the destination address for jumblr’ed funds and doesn’t actually do anything extra.

#### Q. How can I access the jumblr’ed funds?
A. You can login directly to the jumblr’ed funds’ address by prefixing “jumblr “ to your normal passphrase. From there you can directly spend the jumbled funds, but be careful to not commingle such spending with your other funds. In order to minimize the chances for this, it is recommended to use basilisk mode when spending jumbled funds.

#### Q. How can I access jumbled funds without using basilisk mode.
A. You can importprivkey the wif into komodod and use sendtoaddress or any other method for spending funds. For people that take privacy seriously, it is recommended to have a dedicated node that only has imported the privkeys for jumblr addresses and not to mix any other addresses. Then by using this node in a private way it maximizes the privacy as there is no way to backtrace the funds to anything other than “some address that jumbled funds”

#### Q. What other smartaddress types are there?
A. Currently there are coin types for DEX atomic swaps into that coin. The type is the coin symbol in lowercase.

#### Q. What does DEXratio do?
A. By default a DEX client requires 99.5% of theoretical value to accept a DEX offer. This sounds good, but in practice it requires an accommodating LP node to offer such a low margin. Using a DEXratio that is lower allows trades to happen faster. However, if you are not in a hurry, then 99.5% will be a way to maximize your funds.

#### Q. Will smartaddress DEX swap happen instantly?
A. No, but this is actually a good thing! With a native DEX you are directly swapping specific undo (unspent coins) in your wallet. This means that the exact amount being swapped needs to be available and there is coin confirmation times involved in any swap. This is in no way instant, but to get blockchain clearing of a trade, requires the blockchain to prevent double spends. So the cost of never giving up control of your coins and always being able to have 100% access to them is that swapping them takes longer.

However, if you are aware of “dollar cost averaging” you will realize that this slower pace actually prevents you from trading at disadvantageous prices. While it is true that you won’t be able to get the absolute best price that appears in the market for brief moments, the practical question is if you are currently achieving this via centralized exchanges. 99% of day traders lost most all their capital as they chase the deal of the day, or let their emotions determine their trades.

The smartaddress method of trading turns the slower pace of trading into an advantage. Instead of being stuck with whatever the seemingly random market price was the moment you thought to submit a trade, smartaddress trading will get you the cost averaged price as it will incrementally complete the trades over time. 

This also means that you don’t have to spend hours nervously watching the ticker and confusing charts. Smartaddresses are designed for the longer term investor instead of for the day trader.

#### Q. How long will it take to finish the DEX trades?
A. Each DEX trade should finish in a couple of coin confirmations, and for small amounts or trusted LP nodes, it can even complete within a minute. But that is for a single trade. If a large amount is sent to a smartaddress, it is broken up into standard sizes and traded one at a time. 10000 KMD is the largest increment, so if you send in more than 10000 KMD worth of coins, it will do increments of 10000 KMD at a time until there is less. Then it does the 1000 KMD increments in the same way. Finally, it does things in 100 KMD increments.

#### Q. Can I DEX less than 100 KMD worth?
A. No, since a native DEX is issuing multiple (permanent) blockchain transactions, it really isn’t a good thing to do very small trades. That will lead to blockchain bloat and really, if you aren’t even trading 100 KMD (~$25 at current market prices), then it does not seem like any urgent thing to achieve. $25 is within the reach of most people and one millionth the coin supply was arbitrarily set as the minimum DEX increment. Also, consider that if you are trading in lots of $1, then why not just use a central exchange? It won’t be any large disaster regardless of what happens to it.