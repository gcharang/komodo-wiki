For OSx instructions: https://github.com/KomodoPlatform/komodo/wiki/Installing-Komodo-Manually#installing-komodo-on-osx

# Installing Komodo on Ubuntu/Debian

### Requirements

Currently, you will need:

- Linux (easiest with a Debian-based distribution)
- 64-bit
- 4GB of free memory


### Get Started
Log in as the user to your system, and issue these commands to make sure your Linux machine is up to date.

```shell
sudo apt-get update
sudo apt-get upgrade (and say Y when it wants to upgrade stuff)
```

#### Install the dependency packages:
```shell
sudo apt-get install build-essential pkg-config libc6-dev m4 g++-multilib autoconf libtool ncurses-dev unzip git python zlib1g-dev wget bsdmainutils automake libboost-all-dev libssl-dev libprotobuf-dev protobuf-compiler libgtest-dev libqt4-dev libqrencode-dev libdb++-dev ntp ntpdate vim software-properties-common curl libcurl4-gnutls-dev cmake clang
```

### Install `nanomsg`

#### Linux
```shell
cd ~
git clone https://github.com/nanomsg/nanomsg
cd nanomsg
cmake . -DNN_TESTS=OFF -DNN_ENABLE_DOC=OFF
make -j2
sudo make install
sudo ldconfig
```

This takes some time depending your internet connection. Let it run in the background.
Now it is time to install Komodo. Follow each line step by step and ignore the "libgmp headers missing" at some point!

### Installing Komodo

```shell
cd ~
git clone https://github.com/jl777/komodo
cd komodo
git checkout beta
./zcutil/fetch-params.sh
```

`-j8` uses 8 threads - replace 8 with number of threads you want to use or `nproc` variable
```
./zcutil/build.sh -j$(nproc)
```
This can take some time.


#### When it is finished, let's create komodo.conf
```shell
cd ~
mkdir .komodo
cd .komodo
nano komodo.conf
```

Add the following lines to the komodo.conf file (replace rpcuser and rpcpassword)
```
rpcuser=bitcoinrpc
rpcpassword=password
txindex=1
bind=127.0.0.1
rpcbind=127.0.0.1
```

- Press CTRL+O to save the changes.
- Press CTRL+X to exit nano editor.


Now you can start `komodod` daemon to sync with the network
```
cd ~
cd komodo
./src/komodod -addnode=78.47.196.146 -daemon
```

You might see some outputs in terminal where you started `komodod` daemon. So, open a new tab or new terminal window and go to Komodo data directory to see updated logs of Komodo:
```shell
cd ~/.komodo/
tail -f debug.log
```

After `tail` command it will start showing logs update to you as it syncs with the network. If you want to terminate this command just press `CTRL+C`.

In another terminal window you can go to Komodo source directory and use `komodo-cli` command to check the latest update info. like:
```shell
cd ~/komodo/src/
./komodo-cli getinfo
```

This will show you latest info of blockchain and wallet like this:
```shell
{
    "version" : 1000550,
    "protocolversion" : 170002,
    "KMDversion" : "0.1.0",
    "notarized" : 186670,
    "notarizedhash" : "000000308845da840ab9af6c1e09dc02f3118683df065b5ec00b05c9bd58cdae",
    "notarizedtxid" : "6723a10ef4fceab230d4245305d1ed2a916e435abb83269c20daad9bbefd3f0e",
    "notarizedtxid_height" : "mempool",
    "notarized_confirms" : 0,
    "walletversion" : 60000,
    "balance" : 0,
    "interest" : 0,
    "blocks" : 186773,
    "longestchain" : 308867,
    "timeoffset" : 0,
    "tiptime" : 1486411069,
    "connections" : 8,
    "proxy" : "",
    "difficulty" : 1624278.62879530,
    "testnet" : false,
    "keypoololdest" : 1482746526,
    "keypoolsize" : 101,
    "paytxfee" : 0.00000000,
    "relayfee" : 0.00001000,
    "errors" : ""
}
```

in this output when you see `"blocks"` and `"longestchain"` values showing same, your wallet is in full sync.

## Updating Komodo installation to latest version
If you had already installed Komodo from it's source code on your machine, and need to update to latest version just do these commands

```shell
cd ~/komodo/src/
git checkout beta
git pull
make -j$(nproc)
```

if you get any error executing `make` command, then do these commands:
```shell
cd ~/komodo/
git checkout beta
git pull
./zcutil/build.sh -j$(nproc)
```

`build.sh` method will take longer as compare to `make` command. `make` command should work every time for you, butin case it doesn't just use `build.sh` method.


## IMPORTANT: Backup your wallet
We can not stress enough to take a backup of your wallet.dat file time to time. Here's the reason why:

- When you send some funds from an address, sometimes the funds used from the unspent transaction outputs (utxo) leaves a change behind. This change doesn't go back to the same address the funds sent from. This change goes to a new address. And this new address is stored in the `wallet.dat` file located in Komodo data directory on your machine.
- Sometime your wallet.dat file may got corrupted. It's always good to have backup handy.

If you are not sure when to take backup of your wallet.dat file, just take backup of it according to how often your use. If you use it regularly, then just take a backup of your wallet.dat file at then end of day. If not so often then maybe twice a week or depending on your use adjust your time period of taking backup.

You can find your wallet.dat file under linux at `~/.komodo/wallet.dat`.

To backup you can take a copy of this file and make archive of it.
```shell
# To copy
cp -av ~/.komodo/wallet.dat ~/wallet.dat

# To rename file
mv ~/wallet.dat ~/wallet_backup_DATE_HERE.dat

# example
mv ~/wallet.dat ~/wallet_backup_21May2017.dat

# To make archive
tar -czvf ~/wallet_backup_21May2017.dat.tgz ~/wallet_backup_21May2017.dat
```

# Installing Komodo on OSX

### Requirements
Packages are installed through homebrew, make sure to install it: 
```shell
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Now install the dependency packages:
```shell
brew tap discoteq/discoteq; brew install flock
brew install autoconf autogen automake
brew install gcc@6
brew install binutils
brew install protobuf
brew install coreutils
brew install wget
brew install nanomsg
```
or

`brew tap discoteq/discoteq; brew install flock autoconf autogen automake gcc6 binutils protobuf coreutils wget nanomsg`

#### Clone the Komodo repository
```shell
git clone https://github.com/KomodoPlatform/komodo
```

#### Get the proving keys:
```shell
cd komodo
./zcutil/fetch-params.sh
```

#### And now build Komodo
```shell
git checkout dev
./zcutil/build-mac.sh
```
This can take some time, so let's create a configuration file in the mean time. 

### Create configuration file
The configuration file should be created in the following directory:
```shell
~/Library/Application\ Support/Komodo
```
Create the directory if it's missing:
```shell
mkdir ~/Library/Application\ Support/Komodo
```
and create the configuration file by entering this in terminal:
```shell
echo "rpcuser=komodouser" >> ~/Library/Application\ Support/Komodo/komodo.conf
echo "rpcpassword=`head -c 32 /dev/urandom | base64`" >> ~/Library/Application\ Support/Komodo/komodo.conf
echo "txindex=1" >> ~/Library/Application\ Support/Komodo/komodo.conf
echo "bind=127.0.0.1" >> ~/Library/Application\ Support/Komodo/komodo.conf
echo "rpcbind=127.0.0.1" >> ~/Library/Application\ Support/Komodo/komodo.conf
```

### Run Komodo
If the build went well, run komodo:
```shell
cd ~/komodo/src
./komodod -daemon
```

To track progress of downloading the Komodo blockchain:
```shell
tail -f ~/Library/Application\ Support/Komodo/debug.log
```

or get info with the getinfo command:
```shell
./komodo-cli getinfo
```

# Installing Komodo on Windows 64-bit

PLEASE FOLLOW THE VIDEO TUTORIAL: https://youtu.be/gfZZy8b222E



1. First download komodo windows [binaries](https://artifacts.supernet.org/latest/komodo/windows/) and place the files in a new folder on the Desktop called kmd ('C:\Users\YourUserName\Desktop\kmd') .


Open a Command Prompt for the following steps.


2. Next we'll create the Komodo directory in the AppData directory.

`mkdir "%HOMEPATH%\AppData\Roaming\komodo"`


3. Next we will create our komodo.conf file.

`notepad “%HOMEPATH%\AppData\Roaming\Komodo\komodo.conf”`


When Notepad opens click Yes to create the komodo.conf file. Copy the information below and paste it into Notepad. 

> rpcuser=yourRpcUserName

> rpcpassword=yourSecurePassword

> daemon=1

> rpcallowip=127.0.0.1

> rpcbind=127.0.0.1

> server=1

> listen=1

> addnode=5.9.102.210

> addnode=78.47.196.146

> addnode=178.63.69.164

> addnode=88.198.65.74

> addnode=5.9.122.241

> addnode=144.76.94.38

> txindex=1

> maxconnections=1


After pasting, save and exit Notepad.


4. So now that you have created your komodo.conf file you are ready to download the [zk-snark proving key](https://z.cash/downloads/sprout-proving.key) and; [verifying key](https://z.cash/downloads/sprout-verifying.key). 


While the keys are downloading let's paste following command to create the directory for ZcashParams:
HTML

`mkdir “%HOMEPATH%\AppData\Roaming\ZcashParams”`


One the keys have finished downloading we'll paste this command to move the keys to our newly created ZcashParams directory:
HTML

`move “%HOMEPATH%\Downloads\sprout-proving.key” “%HOMEPATH%\AppData\Roaming\ZcashParams” && move “%HOMEPATH%\Downloads\sprout-verifying.key” “%HOMEPATH%\AppData\Roaming\ZcashParams”`


5. Now we can run komodod.exe
HTML

`"%HOMEPATH%\Desktop\kmd\komodod.exe"`


6. Komodod should start synching. You can check progress by running 
HTML

`"%HOMEPATH%\Desktop\kmd\komodo-cli.exe" getinfo`



7. To stop komodod, run: 

`"%HOMEPATH%\Desktop\kmd\komodo-cli.exe" stop`


Downloads:

Windows Binaries: https://artifacts.supernet.org/latest/windows/

Zk-snark proving keys: https://z.cash/downloads/sprout-proving.key

Verifying keys: https://z.cash/downloads/sprout-verifying.key
