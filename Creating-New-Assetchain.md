#### To create and start generating/mining with new Assetchain
```shell
./komodod -ac_name=EXAMPLECHAIN -ac_supply=1000000 -gen
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

TODO