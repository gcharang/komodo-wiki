
How to write utxo based CryptoConditions contracts for KMD chains
by jl777

This is not the only smart contracts methodology that is possible to build on top of OP_CHECKCRYPTOCONDITION, just the first one. All the credit for getting OP_CHECKCRYPTOCONDITION working in the Komodo codebase goes to @libscott. I am just hooking into the code that he made and tried to make it just a little easier to make new contracts.

There is probably some fancy marketing name to use, but for now, I will just call it "CC contract" for short, knowing that it is not 100% technically accurate as the CryptoConditions aspect is not really the main attribute. However, the KMD contracts were built to make the CryptoConditions codebase that was integrated into it to be more accessible.
Since CC contracts run native C/C++ code, it is turing complete and that means that any contract that is possible to do on any other platform will be possible to create via CC contract.

utxo based contracts are a bit harder to start writing than for balance based contracts. However, they are much more secure as they leverage the existing bitcoin utxo system. That makes it much harder to have bugs that issue a zillion new coins from a bug, since all the CC contract operations needs to also obey the existing bitcoin utxo protocol.

This document will be heavily example based so it will utilize many of the existing reference CC contracts. After understanding this document, you should be in a good position to start creating either a new CC contract to be integrated into komodod or to make rpc based dapps directly.

Chapter 0 - Bitcoin Protocol Basics
Chapter 1 - OP_CHECKCRYPTOCONDITION
Chapter 2 - CC contract basics
Chapter 3 - CC vouts
Chapter 4 - CC rpc extensions
Chapter 5 - CC validation
Chapter 6 - faucet example
Chapter 7 - rewards example
Chapter 8 - assets example
Chapter 9 - dice example
Chapter 10 - lotto example
Chapter 11 - channels example
Chapter 12 - limitless possibilities
Chapter 13 - different languages
Chapter 14 - runtime bindings
Chapter 15 - rpc based dapps



Chapter 0 - Bitcoin Protocol Basics
There are many aspects of the bitcoin protocol that isnt needed to understand the CC contracts dependence on it. Such details will not be discussed. The primary aspect is the utxo, unspent transaction output. Just a fancy name for txid/vout, so when you sendtoaddress some coins, it creates a txid and the first output is vout.0, combine it and txid/0 is a specific utxo.

Of course, to understand even this level of detail requires that you understand what a txid is, but there are plenty of reference materials on that. It is basically the 64 char long set of letters and numbers that you get when you send funds.

Implicit with the utxo is that it prevents double spends. Once you spend a utxo, you cant spend it again. This is quite an important characteristic and while advanced readers will point out chain reorgs can allow a double spend, we will not confuse the issue with such details. The important thing is that given a blockchain at a specific height's blockhash, you can know if a txid/vout has been spent or not.

There are also the transactions that are in memory waiting to be mined, the mempool. And it is possible for the utxo to be spent by a tx in the mempool. However since it isnt confirmed yet, it is still unspent at the current height, even if we are pretty sure it will be spent in the next block.

A useful example is to think about a queue of people lined up to get into an event. They need to have a valid ticket and also to get into the queue. After some time passes, they get their ticket stamped and allowed into the event.

In the utxo case, the ticket is the spending transaction and the event is the confirmed blockchain. The queue is the mempool.



