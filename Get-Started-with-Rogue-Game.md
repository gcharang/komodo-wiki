**This is under heavy development and this guide is specifically for testing. If you find any info is missing or not accurate, please let us know at `cc-rogue` channel in [Komodo Discord](https://komodoplatform.com/discord).**
## Compile `rogue` and Komodo
```shell
sudo apt-get update && sudo apt-get upgrade -y
sudo apt-get install build-essential pkg-config libc6-dev m4 g++-multilib autoconf libtool ncurses-dev unzip git python zlib1g-dev wget bsdmainutils automake libboost-all-dev libssl-dev libprotobuf-dev protobuf-compiler libgtest-dev libqt4-dev libqrencode-dev libdb++-dev ntp ntpdate software-properties-common curl libcurl4-gnutls-dev cmake clang -y
git clone https://github.com/jl777/komodo
cd komodo/src/cc
./makerogue
cd ../../
./zcutil/fetch-params.sh
./zcutil/build.sh -j$(nproc)
```

## Get `pubkey` value
We need to get `pubkey` value for the smartaddress you are going to use the ROGUE wallet with. `pubkey` is needed for CC use. If you already know the pubkey of the address you are going to use, please skip to the [next section](https://github.com/KomodoPlatform/komodo/wiki/Detailed-Guide-to-Start-ROGUE-Chain-and-Play-Dice-Using-CLI#start-ROGUE-with-pubkey-to-play-dice-game).

### Step 1
Start the ROGUE chain with the following command and wait for the daemon to fully sync.
```shell
./komodod -ac_name=ROGUE -ac_supply=1000000 -addnode=5.9.102.210  -ac_cclib=rogue -ac_perc=10000000 -ac_reward=100000000 -ac_cc=60001 -ac_script=2ea22c80203d1579313abe7d8ea85f48c65ea66fc512c878c0d0e6f6d54036669de940febf8103120c008203000401cc > /dev/null &
```

### Step 2
Get a new address
```shell
./komodo-cli -ac_name=ROGUE getnewaddress
```

### Step 3
use `validateaddress` command with the address you got to get the pubkey displayed
```shell
./komodo-cli -ac_name=ROGUE validateaddress RPCeZmqW4Aam52DFLmMraWtu5CuXPxqk92
```
This will display the following info from where you get the pubkey.
```JSON
{
  "isvalid": true,
  "address": "RPCeZmqW4Aam52DFLmMraWtu5CuXPxqk92",
  "scriptPubKey": "76a91498b5caa42ffe9868844c51ba6e085e5e7e92fc2588ac",
  "segid": 13,
  "ismine": true,
  "iswatchonly": false,
  "isscript": false,
  "pubkey": "02f183a71e93dfa7672ce7212187e45eabcf4077fed575348504b20295751ab1a2",
  "iscompressed": true,
  "account": ""
}
```
### Step 4
Set the pubkey we got from Step 3. This step is mandatory and without pubkey set, CC is not usable.
```shell
./komodo-cli -ac_name=ROGUE setpubkey 02f183a71e93dfa7672ce7212187e45eabcf4077fed575348504b20295751ab1a2
```
## How to start playing?
### Step 1
Open a new terminal and navigate to `~/komodo/src` directory
```
cd ~/komodo/src
```
### Step 2
Register a new game with single player and 0 buyin. If you don't have funds, this is where you start. `17` is the method that you always have to use. Don't change this value. Otherwise, it will not work.
```
./komodo-cli -ac_name=ROGUE cclib newgame 17 "[1]"
```
Output:
```
{
  "name": "rogue",
  "method": "newgame",
  "maxplayers": 1,
  "buyin": 0.00000000,
  "type": "newbie",
  "hex": "0400008085202f89010061c9741f0451fcbec05ff789eef49487f4e50dcfbe05534b3f37167e9be400010000007b4c79a276a072a26ba067a56580210223b2b9d35fb6383bbbc0dd6668825c91713bc21081b9ce33df3d7edbafa883058140aa48a0604d4d2eb76efd21639b26897fa3c036edd8dd4ca3d91c1f9cce294ec55071aab6187326ee1b1e80a1a3d22f72dd393fb65f009a619e8cf7fb0632a52ca100af03800111a10001ffffffff061027000000000000302ea22c80203d1579313abe7d8ea85f48c65ea66fc512c878c0d0e6f6d54036669de940febf8103120c008203000401cc1027000000000000302ea22c80203d1579313abe7d8ea85f48c65ea66fc512c878c0d0e6f6d54036669de940febf8103120c008203000401cc40420f0000000000302ea22c80208958791fdd38bdf532c97f1691fd231a3f1f5c0c3cd28b68d7383c8b1078828e81031210008203000401cc1027000000000000302ea22c80208958791fdd38bdf532c97f1691fd231a3f1f5c0c3cd28b68d7383c8b1078828e81031210008203000401cc00b8880000000000302ea22c80203d1579313abe7d8ea85f48c65ea66fc512c878c0d0e6f6d54036669de940febf8103120c008203000401cc0000000000000000106a0e114700000000000000000100000000000000341d00000000000000000000000000",
  "txid": "09d702b9bf678ee9d4efc29354566b4453e2e4ebdf7bac3496e667e8d435fe70",
  "result": "success"
}
```
Now, broadcast the raw HEX value
```
./komodo-cli -ac_name=ROGUE sendrawtransaction 0400008085202f89010061c9741f0451fcbec05ff789eef49487f4e50dcfbe05534b3f37167e9be400010000007b4c79a276a072a26ba067a56580210223b2b9d35fb6383bbbc0dd6668825c91713bc21081b9ce33df3d7edbafa883058140aa48a0604d4d2eb76efd21639b26897fa3c036edd8dd4ca3d91c1f9cce294ec55071aab6187326ee1b1e80a1a3d22f72dd393fb65f009a619e8cf7fb0632a52ca100af03800111a10001ffffffff061027000000000000302ea22c80203d1579313abe7d8ea85f48c65ea66fc512c878c0d0e6f6d54036669de940febf8103120c008203000401cc1027000000000000302ea22c80203d1579313abe7d8ea85f48c65ea66fc512c878c0d0e6f6d54036669de940febf8103120c008203000401cc40420f0000000000302ea22c80208958791fdd38bdf532c97f1691fd231a3f1f5c0c3cd28b68d7383c8b1078828e81031210008203000401cc1027000000000000302ea22c80208958791fdd38bdf532c97f1691fd231a3f1f5c0c3cd28b68d7383c8b1078828e81031210008203000401cc00b8880000000000302ea22c80203d1579313abe7d8ea85f48c65ea66fc512c878c0d0e6f6d54036669de940febf8103120c008203000401cc0000000000000000106a0e114700000000000000000100000000000000341d00000000000000000000000000
```
Output:
```
09d702b9bf678ee9d4efc29354566b4453e2e4ebdf7bac3496e667e8d435fe70
```
##### For multiplayer use the following command. Replace `10` to `0` if you want `0` buyin or don't have any funds.
```
./komodo-cli -ac_name=ROGUE cclib newgame 17 \"[3,10]\"
```
### Step 3
Check the `gameinfo` with the txid
Usage:
```
./komodo-cli -ac_name=ROGUE cclib gameinfo 17 \"[%22<YOUR_TX_ID>%22]\"
```
Example: 
```
./komodo-cli -ac_name=ROGUE cclib gameinfo 17 \"[%2209d702b9bf678ee9d4efc29354566b4453e2e4ebdf7bac3496e667e8d435fe70%22]\"
```
Output:
```
{
  "name": "rogue",
  "method": "gameinfo",
  "gametxid": "09d702b9bf678ee9d4efc29354566b4453e2e4ebdf7bac3496e667e8d435fe70",
  "result": "success",
  "height": 7462,
  "start": 7462,
  "starthash": "0006b3ede92cd36bd50f7eca8bfffcb9a32254d1f24193517447752b004b582a",
  "seed": 3767108440867690538,
  "run": "./komodo-cli -ac_name=ROGUE cclib register 17 \"[%2209d702b9bf678ee9d4efc29354566b4453e2e4ebdf7bac3496e667e8d435fe70%22]\"",
  "alive": 0,
  "numplayers": 0,
  "maxplayers": 1,
  "buyin": 0.00000000,
  "players": [
  ]
}
```
The above example shows `run` field which shows the exact command to run in terminal to register the gameplay.
### Step 4
Register the game play txid
```
./komodo-cli -ac_name=ROGUE cclib register 17 \"[%2209d702b9bf678ee9d4efc29354566b4453e2e4ebdf7bac3496e667e8d435fe70%22]\"
```
Output:
```
{
  "name": "rogue",
  "method": "register",
  "maxplayers": 1,
  "buyin": 0.00000000,
  "type": "newbie",
  "hex": "0400008085202f890170fe35d4e867e69634ac7bdfebe4e253446b565493c2efd4e98e67bfb902d70902000000a74ca5a281a1a0819ca28194a067a56580210223b2b9d35fb6383bbbc0dd6668825c91713bc21081b9ce33df3d7edbafa8830581407c0a8458a64c5653b279bbff6f50d23474819c720330510f80294a7a6789d6a11bbb49efb610c8402b67d7323be456bd0b7e787856882cb16a58409b05e42e6aa129a5278020446b52761bffb00eaa7a055c9994987ce2120a551fb4dfd01ffae1ffbee6b56b8103020000af03800111a10001ffffffff02301b0f0000000000302ea22c802039784572269885d080d1990f4eea2b3a93b285b10887d66ccc5f63e0026b0be781031210008203000401cc0000000000000000446a42115270fe35d4e867e69634ac7bdfebe4e253446b565493c2efd4e98e67bfb902d709000000000000000000000000000000000000000000000000000000000000000000000000401d00000000000000000000000000",
  "txid": "0896bf6cdabb31d90aa470ba8b85b01193bbca07b44618f8cadc0ed12d4ea749",
  "result": "success"
}
```
Now, broadcast the raw HEX value.
```
./komodo-cli -ac_name=ROGUE sendrawtransaction 0400008085202f890170fe35d4e867e69634ac7bdfebe4e253446b565493c2efd4e98e67bfb902d70902000000a74ca5a281a1a0819ca28194a067a56580210223b2b9d35fb6383bbbc0dd6668825c91713bc21081b9ce33df3d7edbafa8830581407c0a8458a64c5653b279bbff6f50d23474819c720330510f80294a7a6789d6a11bbb49efb610c8402b67d7323be456bd0b7e787856882cb16a58409b05e42e6aa129a5278020446b52761bffb00eaa7a055c9994987ce2120a551fb4dfd01ffae1ffbee6b56b8103020000af03800111a10001ffffffff02301b0f0000000000302ea22c802039784572269885d080d1990f4eea2b3a93b285b10887d66ccc5f63e0026b0be781031210008203000401cc0000000000000000446a42115270fe35d4e867e69634ac7bdfebe4e253446b565493c2efd4e98e67bfb902d709000000000000000000000000000000000000000000000000000000000000000000000000401d00000000000000000000000000
```
Output:
```
0896bf6cdabb31d90aa470ba8b85b01193bbca07b44618f8cadc0ed12d4ea749c
```
### Step 5
Check the `gameinfo` again after registering to get the game start command with seed.
```
./komodo-cli -ac_name=ROGUE cclib gameinfo 17 \"[%2209d702b9bf678ee9d4efc29354566b4453e2e4ebdf7bac3496e667e8d435fe70%22]\"
```
Output:
```
{
  "name": "rogue",
  "method": "gameinfo",
  "gametxid": "09d702b9bf678ee9d4efc29354566b4453e2e4ebdf7bac3496e667e8d435fe70",
  "result": "success",
  "height": 7462,
  "start": 7462,
  "starthash": "0006b3ede92cd36bd50f7eca8bfffcb9a32254d1f24193517447752b004b582a",
  "seed": 3767108440867690538,
  "run": "cc/rogue/rogue 3767108440867690538 09d702b9bf678ee9d4efc29354566b4453e2e4ebdf7bac3496e667e8d435fe70",
  "alive": 1,
  "numplayers": 1,
  "maxplayers": 1,
  "buyin": 0.00000000,
  "players": [
    {
      "slot": 0,
      "status": "alive",
      "baton": "0896bf6cdabb31d90aa470ba8b85b01193bbca07b44618f8cadc0ed12d4ea749",
      "batonaddr": "R9dCYMKsDQRCg5CLpvThRser1gbBTmkEHG",
      "batonvout": 0,
      "batonvalue": 0.00990000,
      "batonht": 7469
    }
  ]
}
```
Along with the run command, now this shows player data as well.
### Step 6
Start your game using the game start command from the `run` field above
```
cc/rogue/rogue 3767108440867690538 09d702b9bf678ee9d4efc29354566b4453e2e4ebdf7bac3496e667e8d435fe70
```

#### Useful read:
Here is the doc on how to play `rogue` game: https://github.com/jl777/komodo/blob/jl777/src/cc/rogue/rogue.doc
Here are some description on how it works: https://github.com/jl777/komodo/blob/jl777/src/cc/rogue_rpc.cpp

## Important!
This game is being developed. There are bugs present. If you find any bugs, please report them to `cc-rogue` channel in [Komodo Discord](https://komodoplatform.com/discord)