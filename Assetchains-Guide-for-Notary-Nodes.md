#### If you were looking for the readme how to setup the assetchains, don't look further. It is integrated into Komodo now.

This is a small troubleshoot page how to act when some issues comes above.

### How do I start the assetchains?

```
cd ~/komodo/src
./assetchains
```

### Clear all assetchain data


`cd ~/komodo/src`   
`./clearassets`


### I get "Error: Cannot obtain a lock on data directory /home/komodo/.komodo/USD. Zcash is probably already running." when i run it

```
cd ~/komodo/src
fiat/usd stop
```

### How can i see if i'm in sync?

```
cd ~/komodo/src
./fiat-cli getbestblockhash
```
This should result in:
```
aud
0f0a9872cf478e3f5445e0c9d512357fd47002ceceda74c55cf3a6b8f21042e5
bgn
002673e9cb30151b949333aed22c738d026e827b4f5593cbf5cb985a788327dd
cad
0048787568bbdaae1bfd54cf51765e655c55804bdfab49b72172fc0b9ac749bb
chf
091abb7657c52d3d017347905208cf3802d895e10fc1fa3fd2ab1d016a6b443c
cny
007d4f6f62fc4009b9d910b25d1fc8515084ef34b8be2c45ae90e42630c8ac9b
czk
09fe9165ac569cf80e08e584a9fa88f9712f4765b62697df70c461ee32ad3680
dkk
059385780a8cf3a2fbe41145f24ded6e3fe33aef2c5d6cd6675633829325b542
eur
097bfe9283369d9dd2404aa5cce4d2bca8577b9df3f0af0058e01b80ce32c304
gbp
0c2ca033f67c77058bc8b58cc15af2d8f73ae0a152f87434bd74e92768835734
hkd
058bb1c65fa3ff7ff2d2d1d4c8e2fbff719dfe36fa60d676f7dbfcd941571808
hrk
0ddb965a917c2e2f50506177c7ee5d5b202fd7879afb1961a45ededabba4f469
huf
03db0df4790467d01529c76835bbced53a56542818c7fd151352df4e114615c7
idr
0d95c76ede82889057471f0e22e12fbb5d4892c53699d8897ff58a7b34a73946
ils
06b2afd89ca3a25de960fc3cb374d64feafa45fb123fcef24c96c41b19f406d4
inr
0ddc352f2b5d1bb55b1ca6a57968e263c17502fbd7210a2961dc2e4c478b78cb
jpy
008f5b891603631108c196a585987f02f242c932dd594d417d1a4eb4676ac08a
krw
0c055d44a9ef4c55bd8520531e5e3141256f813dfc4b5d4871389ba7f5c70e10
mxn
057b3d7d52035bee8af82181be8dded2043091d02db8623aff4e3adc158e2ec9
myr
0a625c169adc83f9478f5dc4201118edbfac88dd717cc8196f670651f1b388fe
nok
0c0f69ad05f8ea73e4e7f1f5a8ccf70522f84a3713c6ebde65f4cee68a1b86e4
nzd
05fb59679cd49fa4c3c201be3f28a38b8612c5b851439d67099c15f81665c4e0
php
03ec6d86b3ee9561c962002976cdbcb2d8376bf8d44c36282772a595fa0efe2b
pln
0675ea3a1860ea3e9d9b65228f04b5177250ca8e00e97d64dfca731c5315cc46
brl
0c77dc1db4dd4e6bb920f2bd2ce7a4ca5355f9d229de6a0e2e328a31df2efdf1
ron
0654d3412785d64cdfd6f9601ab5a509a18a11a64910ab258b0197bbee7c177a
rub
0c66adad4d7ddbcf9205c9c670c78948f39acd89e1b8d35c9eaa9142fbe379b8
sek
04ce214cb2ec859152045d073b20d2e0d39fc4d828b4b6cff472f57509984aba
sgd
0a4e509d60b125bbe7bd4d5597fee89fcc135f5e830e1415047426d3c1a990d9
thb
0586f5a8717fa643d0d720ceb026d80b78e9c2af6b0bfd711cdb6be13f0a63f4
try
05a947ffec53ee30fc4a18c8ed0f92a6d0c0a28d30d3b2bdbee887533e0f4655
usd
090234753d3a569c8a6e65834be05f536520475c655a70858b26f6c47dd9deda
zar
06c6a433fcca7f9037c60bfa003977fde6dbba9f564543ae0886f9750b175b8f
```

### I am not in sync with a few hashes. what do i have to do now?

Stop the corresponding fiat value and start it again. For instance USD:

```
cd ~/komodo/src
fiat/usd stop
rm -rf ~/.komodo/USD
./komodod -ac_name=USD -addnode=78.47.196.146 &
```

### It is running multiple komodod's when i look into (h)top, is that normal?
Yes, each fiat value starts a komodod task

### How to stop all of them?

```
cd ~/komodo/src
./fiat-cli stop
```

### I receive "error i.1 vs n.2" in the output when i start ./assetchains. Harmless errors, no problem with it. Let it run.

### I can't get it to run?

```
# make sure all komodod / iguana processes are stopped
cd ~/komodo/src/
git pull
make -j8
cd ~/.komodo/
mv komodo.conf ../
mv wallet.dat ../
rm -rf *
mv ../komodo.conf .
mv ../wallet.dat .
cd ..
./start
# wait for resync
cd ~/KomodoPlatform/iguana/
./m_notary
# Wait a couple minutes
cd ~/komodo/src/
./assetchains
./fiat-cli importprivkey U****** (BTCDwif)
# wait for it to finish
./assetfunds btcd address
./fiat-cli stop
./assetchains
```

### Here is how i fixed my nodes to work with assetchains

1 Reboot the server

2 Update Komodo

```
cd komodo && git fetch && git checkout beta && git pull && make -j8
```

3 Delete files from the .komodo folder

```
cd ~/.komodo
rm -rf blocks chainstate debug.log komodostate db.log
```

4 Start the ./start script to resync the komodo chain

```
cd
./start
```

5 Wait till resync is done, this time it is a lot faster

6 Go to ~/komodo/src

```
cd ~/komodo/src
```

7 Edit clearassets and delete everything in it

```
vi clearassets (and delete everything in it)
```

8 And paste the following in it:

```
rm -rf ~/.komodo/USD
rm -rf ~/.komodo/EUR
rm -rf ~/.komodo/JPY
rm -rf ~/.komodo/GBP
rm -rf ~/.komodo/AUD
rm -rf ~/.komodo/CAD
rm -rf ~/.komodo/CHF
rm -rf ~/.komodo/NZD
rm -rf ~/.komodo/CNY
rm -rf ~/.komodo/RUB
rm -rf ~/.komodo/MXN
rm -rf ~/.komodo/BRL
rm -rf ~/.komodo/INR
rm -rf ~/.komodo/HKD
rm -rf ~/.komodo/TRY
rm -rf ~/.komodo/ZAR
rm -rf ~/.komodo/PLN
rm -rf ~/.komodo/NOK
rm -rf ~/.komodo/SEK
rm -rf ~/.komodo/DKK
rm -rf ~/.komodo/CZK
rm -rf ~/.komodo/HUF
rm -rf ~/.komodo/ILS
rm -rf ~/.komodo/KRW
rm -rf ~/.komodo/MYR
rm -rf ~/.komodo/PHP
rm -rf ~/.komodo/RON
rm -rf ~/.komodo/SGD
rm -rf ~/.komodo/THB
rm -rf ~/.komodo/BGN
rm -rf ~/.komodo/IDR
rm -rf ~/.komodo/HRK
rm -rf ~/.komodo/BET
rm -rf ~/.komodo/BOTS
rm -rf ~/.komodo/CRYPTO
rm -rf ~/.komodo/DEX
rm -rf ~/.komodo/HODL
rm -rf ~/.komodo/JUMBLR
rm -rf ~/.komodo/MGW
rm -rf ~/.komodo/PANGEA
rm -rf ~/.komodo/REVS
rm -rf ~/.komodo/SHARK
rm -rf ~/.komodo/SUPERNET
```

9 Save it and run clearassets

```
./clearassets
```

10 Run assetchains

```
./assetchains
```

11 Wait for it until it stops doing its thing. The import your BTCDwif key

```
./fiat-cli importprivkey U*************************** (your BTCDwif key)
```

12 After it is finished fund your assets with your BTCD address

```
./assetfunds R********************* (your BTCD address)
```

13 When that is done, start notarizing

```
cd
cd KomodoPlatform/iguana
git fetch && git checkout beta && ./m_notary
```

14 When this is done you should get the following running all the time

```
GOT DEX PACKET.1079
```

15 Wait half an hour and somewhere in your iguana output you should see the following:

```
[45] ips.55 KMD NOTARIZE.11 matches.17 paxmatches.34 bestmatches.7 bestk.13 1820251030003000 recv.fd37ffd5fb183f70 sigmasks.(0 0) senderind.9 state.0 (98349c0d 0 0) pax.dfc61f91
```

16 When you see pax.dfc61f91 you are ready!!!
