# Komodo Notary Nodes

Note: This is still in *BETA phase*. This is to give you a good understanding on building a Komodo Notary Node, but it is possible that some commands could be deprecated by the time you read it. 

If you have any problems, please join #notarynode on the [Komodo Discord](https://discord.gg/SCdf4eh)

# Requirements

## Hardware

Komodo currently only works on Linux. To setup Komodo Notary Node be sure you have a good solid server with the following requirements:

 - CPU: A good CPU (Xeon/i7)
 - RAM: 64GB
 - Disk: 500GB SSD
 - Bandwidth: 100Mbps

## Operating System

Ubuntu LTS x64 - minimal installation with Openssh server.

## Security

*Before doing anything further, please ensure that your server is secure.*

- Update the Operating System: `sudo apt-get -y update && sudo apt-get -y upgrade`
- Install [Fail2ban](https://www.digitalocean.com/community/tutorials/how-to-protect-ssh-with-fail2ban-on-ubuntu-14-04). 
- [Perform Initial Setup for creating new user, give sudo permission, change SSH port, disable `root` login, disable password authentication for login](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-16-04)
- Please run processes as an unprivileged user and use sudo where necessary


## Install Dependencies
Install regular deps
```shell
sudo apt-get update && sudo apt-get upgrade -y
sudo apt-get install build-essential pkg-config libc6-dev m4 g++-multilib autoconf libtool ncurses-dev unzip git python zlib1g-dev wget bsdmainutils automake libboost-all-dev libssl-dev libprotobuf-dev protobuf-compiler libgtest-dev libqt4-dev libqrencode-dev libdb++-dev ntp ntpdate software-properties-common curl libcurl4-gnutls-dev cmake clang libsodium-dev jq htop -y
```
Install `nanomsg` (requires for iguana)
```shell
cd ~
git clone https://github.com/nanomsg/nanomsg
cd nanomsg
cmake . -DNN_TESTS=OFF -DNN_ENABLE_DOC=OFF
make -j2
sudo make install
sudo ldconfig
```

## Compile Komodo from source
#### Clone source, checkout `beta` branch and compile
```shell
cd ~
git clone https://github.com/jl777/komodo
cd komodo
git checkout beta
./zcutil/fetch-params.sh
./zcutil/build.sh -j$(nproc)
```
`-j$(nproc)` uses all available processor threads while compiling. If you don't want to use all threads, you can use `-j8` which will use only 8 threads.

#### Symlink the compiled binaries
```shell
sudo ln -sf /home/$USER/komodo/src/komodo-cli /usr/local/bin/komodo-cli
sudo ln -sf /home/$USER/komodo/src/komodod /usr/local/bin/komodod
```
#### Create data dir, `komodo.conf` file and secure it
```shell
cd ~
mkdir .komodo
nano ~/.komodo/komodo.conf
```
Insert the following contents inside `komodo.conf` file and save it. (change the `rpcuser` and `rpcpassword` values)
```JSON
rpcuser=usernameChangeItToSomethingSecure
rpcpassword=passwordChangeItToSomethingSecure
txindex=1
server=1
daemon=1
rpcworkqueue=256
```
Secure the `komodo.conf` file
```
chmod 600 ~/.komodo/komodo.conf
```
## Compile Bitcoin and other 3rd-party Coin from Source
### Bitcoin
#### Step 1: Clone Bitcoin source
```shell
cd ~
git clone https://github.com/bitcoin/bitcoin
cd bitcoin
git checkout 0.16
```
#### Step 2: Create a build script called `build.sh` inside the `~/bitcoin` dir for easy compiling and put the contents below inside the script. The script will also symlink the binaries with `/usr/local/bin/` and for that, you will be asked to provide `sudo` password.
```shell
#!/bin/bash
berkeleydb () {
    BITCOIN_ROOT=$(pwd)
    BITCOIN_PREFIX="${BITCOIN_ROOT}/db4"
    mkdir -p $BITCOIN_PREFIX
    wget -N 'http://download.oracle.com/berkeley-db/db-4.8.30.NC.tar.gz'
    echo '12edc0df75bf9abd7f82f821795bcee50f42cb2e5f76a6a281b85732798364ef db-4.8.30.NC.tar.gz' | sha256sum -c
    tar -xzvf db-4.8.30.NC.tar.gz
    cd db-4.8.30.NC/build_unix/

    ../dist/configure -enable-cxx -disable-shared -with-pic -prefix=$BITCOIN_PREFIX

    make install
    cd $BITCOIN_ROOT
}

buildBITCOIN () {
    git pull
    make clean
    ./autogen.sh
    ./configure LDFLAGS="-L${BITCOIN_PREFIX}/lib/" CPPFLAGS="-I${BITCOIN_PREFIX}/include/" --with-gui=no --disable-tests --disable-bench --without-miniupnpc --enable-experimental-asm --enable-static --disable-shared
    make -j$(nproc)
}

cd ~/bitcoin
berkeleydb
buildBITCOIN
sudo ln -sf /home/$USER/bitcoin/src/bitcoin-cli /usr/local/bin/bitcoin-cli
sudo ln -sf /home/$USER/bitcoin/src/bitcoind /usr/local/bin/bitcoind
```
#### Step 3: Make the script executable and run it
```shell
chmod +x build.sh
./build.sh
```
#### Step 4: Create Bitcoin data dir, `bitcoin.conf` file and secure it
```shell
cd ~
mkdir .bitcoin
nano ~/.bitcoin/bitcoin.conf
```
Insert the following contents inside `bitcoin.conf` file and save it. (change the `rpcuser` and `rpcpassword` values)
```JSON
server=1
daemon=1
txindex=1
rpcuser=bitcoinrpc
rpcpassword=password
bind=127.0.0.1
rpcbind=127.0.0.1
```
secure the `bitcoin.conf` file
```shell
chmod 600 ~/.bitcoin/bitcoin.conf
```

### CHIPS
#### Step 1: Clone CHIPS source
```shell
cd ~
git clone https://github.com/jl777/chips3
cd chips3
git checkout dev
```
#### Step 2: Create a build script called `build.sh` inside the `~/chips3` dir for easy compiling and put the contents below inside the script. The script will also symlink the binaries with `/usr/local/bin/` and for that, you will be asked to provide `sudo` password.
```shell
#!/bin/bash
berkeleydb () {
    CHIPS_ROOT=$(pwd)
    CHIPS_PREFIX="${CHIPS_ROOT}/db4"
    mkdir -p $CHIPS_PREFIX
    wget -N 'http://download.oracle.com/berkeley-db/db-4.8.30.NC.tar.gz'
    echo '12edc0df75bf9abd7f82f821795bcee50f42cb2e5f76a6a281b85732798364ef db-4.8.30.NC.tar.gz' | sha256sum -c
    tar -xzvf db-4.8.30.NC.tar.gz
    cd db-4.8.30.NC/build_unix/

    ../dist/configure -enable-cxx -disable-shared -with-pic -prefix=$CHIPS_PREFIX

    make install
    cd $CHIPS_ROOT
}

buildCHIPS () {
    git pull
    make clean
    ./autogen.sh
    ./configure LDFLAGS="-L${CHIPS_PREFIX}/lib/" CPPFLAGS="-I${CHIPS_PREFIX}/include/" --with-gui=no --disable-tests --disable-bench --without-miniupnpc --enable-experimental-asm --enable-static --disable-shared
    make -j$(nproc)
}

cd ~/chips3
berkeleydb
buildCHIPS
sudo ln -sf /home/$USER/chips3/src/chips-cli /usr/local/bin/chips-cli
sudo ln -sf /home/$USER/chips3/src/chipsd /usr/local/bin/chipsd
```
#### Step 3: Make the script executable and run it
```shell
chmod +x build.sh
./build.sh
```
#### Step 4: Create CHIPS data dir, `chips.conf` file and secure it
```shell
cd ~
mkdir .chips
nano ~/.chips/chips.conf
```
Insert the following contents inside `chips.conf` file and save it. (change the `rpcuser` and `rpcpassword` values)
```JSON
server=1
daemon=1
txindex=1
rpcuser=user
rpcpassword=password
addnode=5.9.253.195
addnode=74.208.210.191
```
secure the `chips.conf` file
```shell
chmod 600 ~/.chips/chips.conf
```

### GameCredits (GAME)
#### Step 1: Clone GAME source
```shell
cd ~
git clone https://github.com/gamecredits-project/GameCredits
cd GameCredits
git checkout master
```
#### Step 2: Create a build script called `build.sh` inside the `~/GameCredits` dir for easy compiling and put the contents below inside the script. The script will also symlink the binaries with `/usr/local/bin/` and for that, you will be asked to provide `sudo` password.
```shell
#!/bin/bash
# GameCredits build script for Ubuntu & Debian 9 v.3 (c) Decker

# Step 1: Build BDB 4.8
GAMECREDITS_ROOT=$(pwd)
GAMECREDITS_PREFIX="${GAMECREDITS_ROOT}/db4"
mkdir -p $GAMECREDITS_PREFIX
wget -N 'http://download.oracle.com/berkeley-db/db-4.8.30.NC.tar.gz'
echo '12edc0df75bf9abd7f82f821795bcee50f42cb2e5f76a6a281b85732798364ef db-4.8.30.NC.tar.gz' | sha256sum -c
tar -xzvf db-4.8.30.NC.tar.gz
cd db-4.8.30.NC/build_unix/

../dist/configure -enable-cxx -disable-shared -with-pic -prefix=$GAMECREDITS_PREFIX

make -j$(nproc)
make install
cd $GAMECREDITS_ROOT

# Step 2: Build OpenSSL (libssl-dev) 1.0.x
version=1.0.2o
mkdir -p openssl_build
wget -qO- http://www.openssl.org/source/openssl-$version.tar.gz | tar xzv
cd openssl-$version
export CFLAGS=-fPIC
./config no-shared --prefix=$GAMECREDITS_ROOT/openssl_build
make -j$(nproc)
make install
cd ..

export PKG_CONFIG_PATH="$GAMECREDITS_ROOT/openssl_build/pkgconfig"
export CXXFLAGS+=" -I$GAMECREDITS_ROOT/openssl_build/include/ -I${GAMECREDITS_PREFIX}/include/"
export LDFLAGS+=" -L$GAMECREDITS_ROOT/openssl_build/lib -L${GAMECREDITS_PREFIX}/lib/ -static"
export LIBS+="-ldl"

# p.s. for Debian added -ldl in LDFLAGS it's enough, but on Ubuntu linker doesn't recognize it, so,
# we moved -ldl to LIBS and added -static to LDFLAGS, because linker on Ubuntu doesn't understan that
# it should link librypto.a statically.
#
# Or we can build OpenSSL 1.0.x as shared (instead of no-shared) and use:
# export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/$USER/GameCredits/openssl_build/lib before
# starting gamecreditsd.

# Step 3: Build GameCredits daemon
./autogen.sh
./configure --with-gui=no --disable-tests --disable-bench --without-miniupnpc --enable-experimental-asm --enable-static --disable-shared
make -j$(nproc)

sudo ln -sf /home/$USER/GameCredits/src/gamecredits-cli /usr/local/bin/gamecredits-cli
sudo ln -sf /home/$USER/GameCredits/src/gamecreditsd /usr/local/bin/gamecreditsd
```
#### Step 3: Make the script executable and run it
```shell
chmod +x build.sh
./build.sh
```
#### Step 4: Create GAME data dir, `gamecredits.conf` file and secure it
```shell
cd ~
mkdir .gamecredits
nano ~/.gamecredits/gamecredits.conf
```
Insert the following contents inside `gamecredits.conf` file and save it. (change the `rpcuser` and `rpcpassword` values)
```JSON
server=1
daemon=1
txindex=1
rpcuser=user
rpcpassword=password
bind=127.0.0.1
rpcbind=127.0.0.1
```
secure the `gamecredits.conf` file
```shell
chmod 600 ~/.gamecredits/gamecredits.conf
```

### Einsteinium (EMC2)
#### Step 1: Clone EMC2 source
```shell
cd ~
git clone https://github.com/emc2foundation/einsteinium
cd einsteinium
git checkout master
```
#### Step 2: Create a build script called `build.sh` inside the `~/einsteinium` dir for easy compiling and put the contents below inside the script. The script will also symlink the binaries with `/usr/local/bin/` and for that, you will be asked to provide `sudo` password.
```shell
#!/bin/bash
berkeleydb () {
    EMC2_ROOT=$(pwd)
    EMC2_PREFIX="${EMC2_ROOT}/db4"
    mkdir -p $EMC2_PREFIX
    wget -N 'http://download.oracle.com/berkeley-db/db-4.8.30.NC.tar.gz'
    echo '12edc0df75bf9abd7f82f821795bcee50f42cb2e5f76a6a281b85732798364ef db-4.8.30.NC.tar.gz' | sha256sum -c
    tar -xzvf db-4.8.30.NC.tar.gz
    cd db-4.8.30.NC/build_unix/

    ../dist/configure -enable-cxx -disable-shared -with-pic -prefix=$EMC2_PREFIX

    make install
    cd $EMC2_ROOT
}

buildEMC2 () {
    git pull
    make clean
    ./autogen.sh
    ./configure LDFLAGS="-L${EMC2_PREFIX}/lib/" CPPFLAGS="-I${EMC2_PREFIX}/include/" --with-gui=no --disable-tests --disable-bench --without-miniupnpc --enable-experimental-asm --enable-static --disable-shared
    make -j$(nproc)
}

cd ~/einsteinium
berkeleydb
buildEMC2

sudo ln -sf /home/$USER/einsteinium/src/einsteinium-cli /usr/local/bin/einsteinium-cli
sudo ln -sf /home/$USER/einsteinium/src/einsteiniumd /usr/local/bin/einsteiniumd
```
#### Step 3: Make the script executable and run it
```shell
chmod +x build.sh
./build.sh
```
#### Step 4: Create EMC2 data dir, `einsteinium.conf` file and secure it
```shell
cd ~
mkdir .einsteinium
nano ~/.einsteinium/einsteinium.conf
```
Insert the following contents inside `einsteinium.conf` file and save it. (change the `rpcuser` and `rpcpassword` values)
```JSON
server=1
daemon=1
txindex=1
rpcuser=user
rpcpassword=password
bind=127.0.0.1
rpcbind=127.0.0.1
```
secure the `einsteinium.conf` file
```shell
chmod 600 ~/.einsteinium/einsteinium.conf
```

### GinCoin (GIN)
#### Step 1: Clone GIN source
```shell
cd ~
git clone https://github.com/GIN-coin/gincoin-core
cd gincoin-core
git checkout master
```
#### Step 2: Create a build script called `build.sh` inside the `~/gincoin-core` dir for easy compiling and put the contents below inside the script. The script will also symlink the binaries with `/usr/local/bin/` and for that, you will be asked to provide `sudo` password.
```shell
#!/bin/bash
berkeleydb () {
    GIN_ROOT=$(pwd)
    GIN_PREFIX="${GIN_ROOT}/db4"
    mkdir -p $GIN_PREFIX
    wget -N 'http://download.oracle.com/berkeley-db/db-4.8.30.NC.tar.gz'
    echo '12edc0df75bf9abd7f82f821795bcee50f42cb2e5f76a6a281b85732798364ef db-4.8.30.NC.tar.gz' | sha256sum -c
    tar -xzvf db-4.8.30.NC.tar.gz
    cd db-4.8.30.NC/build_unix/

    ../dist/configure -enable-cxx -disable-shared -with-pic -prefix=$GIN_PREFIX

    make install
    cd $GIN_ROOT
}

buildgin () {
    git pull
    make clean
    ./autogen.sh
    ./configure LDFLAGS="-L${GIN_PREFIX}/lib/" CPPFLAGS="-I${GIN_PREFIX}/include/" --with-gui=no --disable-tests --disable-bench --without-miniupnpc --enable-experimental-asm --enable-static --disable-shared --without-gui
    make -j$(nproc)
}

berkeleydb
buildgin

sudo ln -sf /home/$USER/gincoin-core/src/gincoin-cli /usr/local/bin/gincoin-cli
sudo ln -sf /home/$USER/gincoin-core/src/gincoind /usr/local/bin/gincoind
```
#### Step 3: Make the script executable and run it
```shell
chmod +x build.sh
./build.sh
```
#### Step 4: Create GIN data dir, `einsteinium.conf` file and secure it
```shell
cd ~
mkdir .gincoincore
nano ~/.gincoincore/gincoin.conf
```
Insert the following contents inside `gincoin.conf` file and save it. (change the `rpcuser` and `rpcpassword` values)
```JSON
server=1
daemon=1
txindex=1
rpcuser=user
rpcpassword=password
bind=127.0.0.1
rpcbind=127.0.0.1
```
secure the `gincoin.conf` file
```shell
chmod 600 ~/.gincoincore/gincoin.conf
```

### HUSH3
Clone HUSH3 source and compile
```shell
cd ~
git clone https://github.com/MyHush/hush3
cd hush3
git checkout dev
./zcutil/build.sh -j$(nproc)
```

### VerusCoin (VRSC)
Clone VRSC source and compile
```shell
cd ~
git clone https://github.com/VerusCoin/VerusCoin
cd VerusCoin
git checkout master
./zcutil/build.sh -j$(nproc)
```
---
## Start the daemons and sync all chain

For the first time sync, we will run all the coin daemons normally. Make sure you have successfully compiled all daemon above. We will create a `start` script later on this guide to start the chains for notarisation with `-pubkey`.

#### Start 3rd-party coins
```
bitcoind &
chipsd &
gamecreditsd &
einsteiniumd &
gincoind &
```

#### Start Komodo and all assetchains including VRSC & HUSH3
```
cd ~/komodo/src
./komodod &
./assetchains.old
```

Now wait for all chain to finish sync. You can check check sync progress with `tail -f` command. All coin data dir creates a `debug.log` file, we just need a `tail` that file. Komodo assetchains has their own data dir inside `~/.komodo` directory.

#### Commands to tail `debug.log`
```shell
# BTC
tail -f ~/.bitcoin/debug.log
# CHIPS
tail -f ~/.chips/debug.log
# GAME
tail -f ~/.gamecredits/debug.log
# EMC2
tail -f ~/.einsteinium/debug.log
# GIN
tail -f ~/.gincoincore/debug.log
# KMD
tail -f ~/.komodo/debug.log
# VRSC
tail -f ~/.komodo/VRSC/debug.log
# HUSH3
tail -f ~/.komodo/HUSH3/debug.log
# SUPERNET
tail -f ~/.komodo/SUPERNET/debug.log
```

For any other Komodo assetchain, use the example of VRSC, HUSH3 or SUPERNET and change the path with coin name accordingly. Wait for sync to finish fully for all coins.

---

## Generating `pubkey`, `address` & `WIF` from your secure passphrase
The mainnet operators will have 2 seed phrase (passphrase) which will generate 2 sets of pubkey, address and private key (WIF).
**For security best practices, you should never use your seed phrase or privatekey in another node other than your notary node. If you ever expose a private key for any coin, it can be converted to all other coins easily.**

### Generate
You need to provide 2 sets of pubkey to Kolo when he asks for it (pubkey starts with `02` or `03`). Follow [this guide](https://github.com/KomodoPlatform/komodo/wiki/How-to-Generate-Address-and-Private-Key-(WIF)-for-3rd-Party-Coins-Using-Passphrase) to generate all required info in your server. You will need the compressed address, pubkey and WIF from output generated by the script. Based on the default seed used in the `genkomodo.php` file, we get the following information:
```
Pubkey: 02a854251adfee222bede8396fed0756985d4ea905f72611740867c7a4ad6488c1

BTC Address: 1M68ML9dMZZPEdrjncUCe7ZWadAGUxMNyv
BTC WIF: L24bEAJSkFCdjoQNEcboWfJdsLGLmkBgfGb4TSHnbhEmU9jenaes

KMD Address: RVNKRr2uxPMxJeDwFnTKjdtiLtcs7UzCZn
KMD WIF: UtrRXqvRFUAtCrCTRAHPH6yroQKUrrTJRmxt2h5U4QTUN1jCxTAh

GAME Address: Gdw3mTUaLRAgK7A2iZ8K4suQVnx7VRJ9rf
GAME WIF: Re6YxHzdQ61rmTuZFVbjmGu9Kqu8VeVJr4G1ihTPFsspAjGiErDL

EMC2 Address: EdF2quz8nWrJDwTbbTTieFYUMGfPsVB5dv
EMC2 WIF: T7trfubd9dBEWe3EnFYfj1r1pBueqqCaUUVKKEvLAfQvz3JFsNhs

GIN Address: Gdw3mTUaLRAgK7A2iZ8K4suQVnx7VRJ9rf
GIN WIF: WNejFTXR11LFx2L8wvEKEqvjHkL1D3Aa4CCBdEYQyBzbBKjPLHJQ
```
CHIPS & all Komodo assetchain including HUSH3 & VRSC uses same address and WIF as Komodo (KMD).

It is recommended that you write down 24 words random seed in a piece of paper and type directly into your server. Do not keep the seed saved in your local computer.

### Import private key into coin daemons
**Important: Make sure your daemon is running and fully synced before importing any privkey. Importing key into daemon will trigger rescan which can take some time to finish depending on tx history.**

- Follow the below example to import key into your coin daemons:
```shell
komodo-cli importprivkey UtrRXqvRFUAtCrCTRAHPH6yroQKUrrTJRmxt2h5U4QTUN1jCxTAh
bitcoin-cli importprivkey WNejFTXR11LFx2L8wvEKEqvjHkL1D3Aa4CCBdEYQyBzbBKjPLHJQ
chips-cli importprivkey UtrRXqvRFUAtCrCTRAHPH6yroQKUrrTJRmxt2h5U4QTUN1jCxTAh
gamecredits-cli importprivkey Re6YxHzdQ61rmTuZFVbjmGu9Kqu8VeVJr4G1ihTPFsspAjGiErDL
einsteinium-cli importprivkey T7trfubd9dBEWe3EnFYfj1r1pBueqqCaUUVKKEvLAfQvz3JFsNhs
gincoin-cli importprivkey WNejFTXR11LFx2L8wvEKEqvjHkL1D3Aa4CCBdEYQyBzbBKjPLHJQ
komodo-cli -ac_name=HUSH3 importprivkey UtrRXqvRFUAtCrCTRAHPH6yroQKUrrTJRmxt2h5U4QTUN1jCxTAh
komodo-cli -ac_name=VRSC importprivkey UtrRXqvRFUAtCrCTRAHPH6yroQKUrrTJRmxt2h5U4QTUN1jCxTAh
```
- For all other Komodo assetchains, use the following command to import privkey
```shell
cd ~/komodo/src
./fiat-cli importprivkey UtrRXqvRFUAtCrCTRAHPH6yroQKUrrTJRmxt2h5U4QTUN1jCxTAh
```
This command will import keys into all assetchains that are using the main Komodo daemon. This may take some time and will display the coin name and address after each import. You can tail the coin specific `debug.log` files to check progress.

### Validate the address
After all addresses are imported, using the `$COIN-cli` validate all address and make sure it is showing `ismine:true`. If you have `ismine:false`, dPoW will not work for that coin. Sample command for that is below.
```shell
komodo-cli validateaddress RVNKRr2uxPMxJeDwFnTKjdtiLtcs7UzCZn
```

### Create `pubkey.txt` file
We will need to create a `pubkey.txt` file inside `~/komodo/src/` directory. This will be used to start all assetchains including HUSH3 & VRSC with `-pubkey=` param. `pubkey.txt` file should contain only the below information. Change `02a854251adfee222bede8396fed0756985d4ea905f72611740867c7a4ad6488c1` with your own pubkey.
```
pubkey=02a854251adfee222bede8396fed0756985d4ea905f72611740867c7a4ad6488c1
```

### Stop All Wallet Daemon Safely
Never use `kill -9` to kill any wallet daemon if you don't like corrupt database. Always shutdown wallet daemon and iguana gracefully with `pkill -15 iguana` or use the below RPC commands for wallets.
```
komodo-cli stop
bitcoin-cli stop
chips-cli stop
gamecredits-cli stop
einsteinium-cli stop
gincoin-cli stop
komodo-cli -ac_name=HUSH3 stop
komodo-cli -ac_name=VRSC stop
```
For all other Komodo assetchains, use the following command to `stop` the daemons.
```shell
cd ~/komodo/src
./fiat-cli stop
```

---

## Setting up Iguana
### Clone the source
```shell
cd ~
git clone https://github.com/jl777/SuperNET
cd SuperNET/iguana
git checkout dev
```
#### Copy the `pubkey.txt` file that we created earlier from `~/komodo/src/` dir.
```shell
cp ~/komodo/src/pubkey.txt ~/SuperNET/iguana/pubkey.txt
```
#### Create `wp_7776`
Create `wp_7776` file inside `iguana` dir with your 24 words seed passphrase. The file should look like below (replace `YOUR VERY SECURE PASSPHRASE` with your own):

```curl
curl --url "http://127.0.0.1:7776" --data "{\"method\":\"walletpassphrase\",\"params\":[\"YOUR VERY SECURE PASSPHRASE\", 9999999]}"
```
#### Make `wp_7776` file executable
```shell
chmod +x wp_7776
```
#### Create `userhome.txt`
Create `userhome.txt` file inside `iguana` dir with your server user name. Here is an example using user `dextar` and how the file should look like.
```
home/dextar
```
There shouldn't be any other text or space.

---

## Set `ulimit` parameters on Ubuntu permanent

By default the number of open files per user in Ubuntu is 1024. In our case this number is too small so you have to increase it.

This is done with the `ulimit` command:

```bash
$ulimit -a   # see all the kernel parameters
$ulimit -n   # see the number of open files
$ulimit -n 1000000  #  set the number open files to 1000000
```

The problem with this way is that the `ulimit` parameter is only set currently for this command terminal and user. This means that after a reboot you’ll need to set the parameter again. Do the following to set it permanent:

### Edit `/etc/security/limits.conf` file

```bash
sudo nano /etc/security/limits.conf
```

add these lines:

```bash
* soft nofile 1000000
* hard nofile 1000000
```
Save and close file

### Edit `/etc/pam.d/common-session` file

```bash
sudo nano /etc/pam.d/common-session
```

add this line:

```bash
session required pam_limits.so
```
save and close file

We are done. Now let's stop all our wallet daemons safely with RPC commands and reboot the server using `sudo reboot` or `sudo shutdown -r` command.

### Check the values now:

```bash
ulimit -n
```

---

## Create `start` Script
We need a `start` script in the home dir to start Komodo, assetchains and all 3rd party coin daemons with `-pubkey` option. `-pubkey` is not required for BTC daemon. All other coins needs it. Here is an example of start script (change the pubkey with your Notary Node pubkey):
```shell
bitcoind &
chipsd -pubkey="02a854251adfee222bede8396fed0756985d4ea905f72611740867c7a4ad6488c1" &
gamecreditsd -pubkey="02a854251adfee222bede8396fed0756985d4ea905f72611740867c7a4ad6488c1" &
einsteiniumd -pubkey="02a854251adfee222bede8396fed0756985d4ea905f72611740867c7a4ad6488c1" &
gincoind -pubkey="02a854251adfee222bede8396fed0756985d4ea905f72611740867c7a4ad6488c1" &
cd komodo/src
./komodod -gen -genproclimit=1 -notary -pubkey="02a854251adfee222bede8396fed0756985d4ea905f72611740867c7a4ad6488c1" &
sleep 600
./assetchains
```
Make the file executable:
```bash
chmod +x start
```
### Start the script
This may take upto 20-30 minutes max depending on system spec.
```
cd ~
./start
```
**Make sure all daemons started properly before starting iguana in the next step.**

## Start `iguana`
Once all required daemons are running, we have funds on all coins, we can go ahead and start `iguana`.
```
cd ~/SuperNET/iguana
./m_notary
```
`m_notary` script will issue a `git pull` command to update the repo, remove old iguana, compile fresh and start all the process. This can take about 10 minutes maximum to finish. You will see `INIT with 64 notaries` once the process finishes.

## Start dPoW
After you see `INIT with 64 notaries`, you can safely start dPoW process. Just issue the following command from inside `iguana` dir to start with it.
```
./dpowassets
```
Iguana should split your utxos automatically. If you want to test or split manually, you can follow [this guide](https://github.com/KomodoPlatform/komodo/wiki/How-to-Split-UTXO-for-Notarization).

