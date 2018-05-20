## Purpose:

The perpose of this test is to check if the komodo source from jl777 branch has no issues compiling, all assetchains starts and syncs fully from scratch, no stuck chains.

N.B.: This doc is similar to compiling komodo with additional `verify` script. Please make sure you are not testing this in a node with funds. This doc is intended for test nodes/computer. If you have funds in your assetchains please backup your wallet and private key. The information in this doc may change in the future to update and make it better.

## Steps:

1. Install Dependencies (most needed for the first time)
2. Clone komodo repo and compile
3. Clear all assetchaiins dir .komodo data dir
4. Start AC using `./assetchains.old` and let them sync (it may take a while)
5. Verify the assetchains

## Details:

### 1. Install dependencies

```
sudo apt-get update && sudo apt-get upgrade -y
sudo apt-get install build-essential pkg-config libc6-dev m4 g++-multilib autoconf libtool ncurses-dev unzip git python zlib1g-dev wget bsdmainutils automake libboost-all-dev libssl-dev libprotobuf-dev protobuf-compiler libgtest-dev libqt4-dev libqrencode-dev libdb++-dev ntp ntpdate vim software-properties-common curl libcurl4-gnutls-dev cmake clang -y
cd ~
git clone https://github.com/nanomsg/nanomsg
cd nanomsg
cmake . -DNN_TESTS=OFF -DNN_ENABLE_DOC=OFF
make -j2
sudo make install
sudo ldconfig

```

### 2. Clone and Compile

```
cd ~
git clone https://github.com/jl777/komodo
cd komodo
git checkout jl777
./zcutil/fetch-params.sh
./zcutil/build.sh -j$(nproc)
```

### 3. Clear existing assetchains database and files

```
cd ~/.komodo
rm -rf AXO  BEER  BET  BNTN  BOTS  BTCH  CEAL  CHAIN  COQUI  CRYPTO  DEX  DSEC  ETOMIC  HODL  JUMBLR  KV  MESH  MGW  MNZ  MSHARK  NINJA  OOT  PANGEA  PIZZA  PRLPAY  REVS  SUPERNET  WLC
```

### 4. Start all assetchains

```
cd ~/komodo/src
./assetchains.old
```
> Wait for all sync to finish before proceeding to next step. This may take a while depending your hardware and internet connection

### 5a. Create `verify` script and get `coinlist` file

```
cd ~
wget -qO coinlist https://raw.githubusercontent.com/KomodoPlatform/komodotools/master/webworker01/coinlist
nano verify
# paste the following content into it and save the file
#!/bin/bash

source coinlist
forked=false

blocks=$(komodo-cli getinfo | jq .blocks)
longest=$(komodo-cli getinfo | jq .longestchain)

if ((blocks < longest)); then
    forked=true
    printf "\033[0;31mKMD - Possible fork!\033[0m Blocks $blocks < LongestChain $longest\n"
else
    echo "KMD - Blocks $blocks = LongestChain $longest"
fi

for coins in "${coinlist[@]}"; do
    coin=($coins)

    blocks=$(komodo-cli -ac_name=${coin[0]} getinfo | jq .blocks)
    longest=$(komodo-cli -ac_name=${coin[0]} getinfo | jq .longestchain)
    
    if ((blocks < longest)); then
        forked=true
        printf "\033[0;31m${coin[0]} - Possible fork!\033[0m Blocks $blocks < LongestChain $longest\n"
    else
        echo "${coin[0]} - Blocks $blocks = LongestChain $longest"
    fi
done

if [ "$forked" = false ]; then
    printf "\033[0;32mAll coins are fine\033[0m\n"
fi
```
# change permission of `verify` file
```
chmod +x verify
```

### 5b. Verify the block height

```
./verify
```

Result:
The result will similar to below. At the end of the output you will see `All coins are fine`.

```
KMD - Blocks 842790 = LongestChain 842790
CHIPS - Blocks 1987211 = Headers 1987211
REVS - Blocks 162517 = LongestChain 162517
SUPERNET - Blocks 221024 = LongestChain 221024
DEX - Blocks 179547 = LongestChain 179547
PANGEA - Blocks 18770 = LongestChain 18770
JUMBLR - Blocks 153932 = LongestChain 153932
BET - Blocks 47470 = LongestChain 47470
CRYPTO - Blocks 31645 = LongestChain 31645
HODL - Blocks 65524 = LongestChain 65524
MSHARK - Blocks 152851 = LongestChain 152851
BOTS - Blocks 141093 = LongestChain 141093
MGW - Blocks 21237 = LongestChain 21237
COQUI - Blocks 86948 = LongestChain 86948
WLC - Blocks 224671 = LongestChain 224671
KV - Blocks 59329 = LongestChain 59329
CEAL - Blocks 31095 = LongestChain 31095
MESH - Blocks 20467 = LongestChain 20467
MNZ - Blocks 87188 = LongestChain 87188
AXO - Blocks 8731 = LongestChain 8731
ETOMIC - Blocks 17931 = LongestChain 17931
BTCH - Blocks 49431 = LongestChain 49431
PIZZA - Blocks 31850 = LongestChain 31850
BEER - Blocks 37853 = LongestChain 37853
NINJA - Blocks 8102 = LongestChain 8102
OOT - Blocks 41106 = LongestChain 41106
BNTN - Blocks 39911 = LongestChain 39911
CHAIN - Blocks 2722 = LongestChain 2722
PRLPAY - Blocks 1335 = LongestChain 1335
DSEC - Blocks 230 = LongestChain 0
All coins are fine
```