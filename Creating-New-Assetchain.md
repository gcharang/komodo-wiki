#### To create and start generating/mining with new Assetchain
```shell
./komodod -ac_name=EXAMPLECHAIN -ac_supply=1000000 -gen &
```

The above will start a new assetchain with name "EXAMPLECHAIN" and start generating blocks every 60 seconds.
The first node which spawned a new assetchain will mine first 100 blocks.
And that same first node will also hold the supply of this assetchains' token 1000000.

#### Querying Assetchain
You can query for assetchain blocks and balances with this komodo CLI command:
```shell
./komodo-cli -ac_name=EXAMPLECHAIN getinfo
```


#### Connecting to Assetchain as a node
Now, the other nodes which need to use the same Assetchain will need to connect to the first node which spawned this new Assetchain. For that the client nodes will use this komodo daemon command:
```shell
./komodod -ac_name=EXAMPLECHAIN -ac_supply=1000000 -addnode=<ipaddr of 1st node>
```

So, for example, the first node which spawned this new Assetchain had Public static IPv4 "123.123.123.123". The command for nodes connecting to this assetchain will look like this:
```shell
./komodod -ac_name=EXAMPLECHAIN -ac_supply=1000000 -addnode=123.123.123.123
```

#### Secure this Assetchain with Delayed Proof of Work
You can also setup your own Notary Nodes other than Official Komodo Notary Nodes.

These notary nodes will only be specific to your Assetchain, and will notarize the assetchain blocks to Komodo network. Since Komodo network is secured with DPoW by notarising it's blocks on to Bitcoin blockchain, Assetchains inherit that security via Komodo.

## New `komodod -ac` Parameters

New `komodod -ac` parameters for parallel chains. If `-ac_reward=<satoshis>` is non-zero, the chain will mine normally and start with `-ac_reward` for the block reward.

If `-ac_end=<endheight>` is set, then -ac_reward will be `0` after `endheight` is reached.

If `-ac_halving=<halvingperiod>` is set, then every `<halvingperiod>` blocks the block reward is reduced according to one of three methods. 1440 (approx a day) is the most frequent halving period.

If `-ac_decay` is not set, then the normal bitcoin halving is done.

If `-ac_decay=<numerator>` is set to be exactly `100000000`, then the `-ac_reward` is scaled linearly toward `0`, with 0 at `endheight`.

For all other values of numerator (less than 100000000) the reward is iteratively reduced by the number of "halving" periods, ie.
```c
    numhalvings = (height / -ac_halving);
    for (i=0; i<numhalvings; i++)
        reward = (reward * -ac_decay) / 100000000;
```

If `-ac_perc=<satoshis>` is nonzero and less than equal `100000000` and `-ac_pubkey=<secp_pubkey33>` is set to a 33byte hexstr (len 66 starting with 02 or 03) then the txfee is increased by the commission rate indicated by `-ac_perc`, with 100000000 being the max of 100%. wallets will need to be customised to make sure to pay the `-ac_perc` of transaction size as txfee. chains with a percentage override can only be mined by the `-ac_pubkey address`.

Bitcoin behaviour would be `-ac_reward=5000000000 -ac_halving=210000`
KMD behaviour would be `-ac_reward = 300000000 -ac_end=7777777`

A more smoothly reducing reward that halves every 210000 blocks would be:  
```shell
-ac_reward=5000000000 -ac_halving=10000 -ac_decay=96777000
-ac_cc=1
```
