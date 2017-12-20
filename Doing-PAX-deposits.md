In this practical example we'll see how we can send USD from our Komodo wallet, and what steps does it include to complete this deposit.

Few facts about PAX to know before trying this:

- PAX uses Notary Node network to get a price feed of different fiat currencies to get the price, and in deterministic way through group signed transaction submits it to Komodo blockchain and secures it with delayed Proof of Work (dPoW) by notarizing these price feeds on bitcoin blockchain.
- FIAT chains are special version of Assetchains feature of Komodo, which get the price feed and swap between KMD token and FIAT chain tokens.
- ANY converted FIAT token will always remain same amount in FIAT chain, no matter how much KMD price fluctuates. Means, if at the deposit time you got 100 USD fiat chain tokens by converting KMD, the fluctuation of KMD price vs BTC or other FIAT, cryptocurrency will not change the amount of 100 USD FIAT tokens you hold in your PAX FIAT chain wallet. Converting those FIAT chain tokens will give you less or more or same KMD tokens based on the pricefeed determined on the current Komodo block.
- When depositing USD from KMD wallet, it costs the transaction fee (0.0001) + [(0.0001 or 0.1% of deposit) whichever is greater value]. Later is needed some small fee to prevent spam.
- When you do a paxdeposit, it is a KMD transaction, the FIAT chain has to monitor KMD for the deposits. It also monitors KMD chain for pricefeed info; Though that is subject to change.


>There is a decentralized Multigateway equivalent built into the trio of komodod, fiatchain and Notary DAPP (iguana).
>Best to view fiat chains as emerging out of the combination of the entire system.
>Pure assetchains like REVS doesnt need to run KMD on regular nodes.
> -- jl777

# Depositing USD to PAXUSD fiatchain from Komodo

### query for current block height of KMD:
```shell
root@kmd01:~/komodo/src# komodo-cli getblockcount
69833
```

### Get Pax Price of KMD per USD

#### Example 1:
```JSON
root@kmd01:~/komodo/src# komodo-cli paxprice kmd usd 69833 1
{
    "base" : "kmd",
    "rel" : "usd",
    "height" : 69833,
    "seed" : "14368648884342028619",
    "timestamp" : 1479168854,
    "price" : 0.00819000,
    "invprice" : 122.10012210,
    "basevolume" : 1.00000000,
    "relvolume" : 0.00819000
}
```

base = kmd

rel = usd

height = is taken from previous command of current height

amount = how much amount is being used to calculate the price.


price = is showing in output, which says 1 KMD is equals to USD $0.00819000



#### Example 2:
```JSON
root@kmd01:~/komodo/src# komodo-cli paxprice kmd usd 69833 100
{
    "base" : "kmd",
    "rel" : "usd",
    "height" : 69833,
    "seed" : "14368648884342028619",
    "timestamp" : 1479168854,
    "price" : 0.00819000,
    "invprice" : 122.10012210,
    "basevolume" : 100.00000000,
    "relvolume" : 0.81900000
}
```

Using amount (at the end of input command) $100 USD it shows how much total KMD we'll get.

basevolume: 100.00000000 = means at current block height and price feed in Komodo blockchain, we are calculating how much KMD we'll get

relvolume: 0.81900000 = means we'll get 0.81900000 KMD if we trade $100 USD for it.




### Converting KMD to USD
```JSON
root@kmd01:~/komodo/src# komodo-cli paxprice kmd usd 69833 0.05
{
    "base" : "kmd",
    "rel" : "usd",
    "height" : 69833,
    "seed" : "14368648884342028619",
    "timestamp" : 1479168854,
    "price" : 0.00819000,
    "invprice" : 122.10012210,
    "basevolume" : 0.05000000,
    "relvolume" : 0.00040950
}
root@kmd01:~/komodo/src# komodo-cli paxdeposit YOUR_KOMODO_ADDRESS 0.05 usd
f9510bbd58f90ea133653005a6e3087779fd838317518ba63cce2b0fc381dff6
root@kmd01:~/komodo/src#
```

In this example found the KMD price per USD and got how much of KMD will be spent if need to send $0.05 USD to the address specified in paxdeposit command.

The command spent "0.00040950" KMD, and deposited "0.05" USD to the address "YOUR_KOMODO_ADDRESS". The output is the transaction ID (txid).

As the command is ran from komodo-cli and is from Komodo blockchain we can query the output txid in Komodo blockchain.


### Getting output of txid for this transaction:
```JSON
root@kmd01:~/komodo/src# komodo-cli gettransaction f9510bbd58f90ea133653005a6e3087779fd838317518ba63cce2b0fc381dff6
{
    "amount" : -0.35022700,
    "fee" : -0.00001375,
    "confirmations" : 2,
    "blockhash" : "00052f5b7bf6c088c1714a40fd563d5c2c83253331a94cb35a8f6197371a115e",
    "blockindex" : 1,
    "blocktime" : 1479169769,
    "txid" : "f9510bbd58f90ea133653005a6e3087779fd838317518ba63cce2b0fc381dff6",
    "walletconflicts" : [
    ],
    "time" : 1479169644,
    "timereceived" : 1479169644,
    "vjoinsplit" : [
    ],
    "details" : [
        {
            "account" : "",
            "address" : "YOUR_KOMODO_ADDRESS",
            "category" : "send",
            "amount" : -0.00035022,
            "vout" : 0,
            "fee" : -0.00001375
        },
        {
            "account" : "",
            "category" : "send",
            "amount" : -0.35022700,
            "vout" : 2,
            "fee" : -0.00001375
        },
        {
            "account" : "",
            "address" : "YOUR_KOMODO_ADDRESS",
            "category" : "receive",
            "amount" : 0.00035022,
            "vout" : 0
        }
    ],
    "hex" : "01000000016bc550a4268fd475b0f41b1dc40302c17679ba7aab3d356b9f7e9d38d03c90cb0a0000006a4730440220712afcd691fb864863276b8794baa83b458ddd1781d309cd3e65100930ed05c6022043cf2a9bad46071bc040958a499be62ba9898baf07aee499f645f01a6f3263df012103212a73f5d38a675ee3cdc6e82542a96c38c3d1c79d25a1ed2e42fcf6a8be4e68feffffff03ce880000000000001976a91499dcd2410fff73989942a8d9c319f193ceb9fff788ac97b1c80f000000001976a9143c9a2967e586976c12abfac719b7c9cbfe62e79f88ac6c67160200000000286a264402555344404b4c00000000003c99dcd2410fff73989942a8d9c319f193ceb9fff7d61001005f562a58"
}
```


### Querying USD amount recieved at PAXUSD chain

#### 1. can check the updated balance with command:
```JSON
root@kmd01:~/komodo/src# komodo-cli -ac_name=USD getwalletinfo
{
    "walletversion" : 60000,
    "balance" : 0.10956755,
    "unconfirmed_balance" : 0.00000000,
    "immature_balance" : 0.05000000,
    "txcount" : 11,
    "keypoololdest" : 1479038499,
    "keypoolsize" : 101
}
```

#### 2. Can see list of transactions happened for your PAXUSD wallet
```JSON
root@kmd01:~/komodo/src# komodo-cli -ac_name=USD getwalletinfo
[
	{
        "account" : "",
        "address" : "YOUR_KOMODO_ADDRESS",
        "category" : "immature",
        "amount" : 0.05000000,
        "vout" : 1,
        "confirmations" : 1,
        "generated" : true,
        "blockhash" : "0e041d04f00010196d4b2e4e3948835bf968860e4282d37220957bd6985bb8db",
        "blockindex" : 0,
        "blocktime" : 1479169801,
        "txid" : "d5ce5aa89f4447211fb2e49a44128fe6fee05c63da79d1991c15564115bdffff",
        "walletconflicts" : [
        ],
        "time" : 1479169801,
        "timereceived" : 1479169805,
        "vjoinsplit" : [
        ]
    }
]
```