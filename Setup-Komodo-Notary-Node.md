# Komodo Notary Nodes

_Please note: all of this is still in BETA phase. This readme gives you a good understanding how to build a Komodo Notary Node, but it is possible that some commands are already deprecated. If you have any problems, please join #notarynode on the Komodo-Platform Slack_

# Requirements
## Hardware
Komodo currently only works on Linux. To setup Komodo Notary Node be sure you have a good solid server/node with the following requirements:

 - A good CPU (Xeon/i7)
 - 64GB of RAM
 - 500GB SSD disk
 - 100Mbps internet connection

## Software
Ubuntu 14.04 or 16.04 minimal installation with SSH server (this tutorial is based on 14.04)

Before you start with the whole installation process take care of the security of the server.

## Bitcoin funded Address
You'll also need to have the bitcoin address associated/used with notary node to be funded with some bitcoins.
Each notary node when notarise a block is currently set to use 0.0001 BTC. So, it is best to put some BTC to it if you are participating in testnet of Komodo Notary Nodes.
Example:

`0.0001 * aprox. 144 (btc blocks per day) = aprox. 0.0144`

`0.05 BTC etc. which are equals to aprox 30+ USD.`

## Security Tips

TIP: you don't have to change the SSH port. Follow the tutorials down under and you are set.

- Install fail2ban https://www.digitalocean.com/community/tutorials/how-to-protect-ssh-with-fail2ban-on-ubuntu-14-04
- Create a user and install a SSH key https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-14-04

# Get Started
Log in as the user you made earlier (ssh user@ip_address_of_server)

```
sudo apt-get update
sudo apt-get upgrade (and say Y when it wants to upgrade stuff)
```

The following packages are needed:
```
sudo apt-get install build-essential pkg-config libc6-dev m4 \
		g++-multilib autoconf libtool ncurses-dev unzip git python \
		zlib1g-dev wget bsdmainutils automake libboost-all-dev \
		libssl-dev libprotobuf-dev protobuf-compiler libqt4-dev \
		libqrencode-dev libdb++-dev ntp ntpdate vim software-properties-common \
		curl libcurl4-gnutls-dev cmake clang
```

Something else needs to be done. Libnanomsg needs to be installed. For Ubuntu 14.04 you need to install it yourself. 

```
cd /tmp
wget https://github.com/nanomsg/nanomsg/archive/1.0.0.tar.gz -O nanomsg-1.0.0.tar.gz
tar -xzvf nanomsg-1.0.0.tar.gz
cd nanomsg-1.0.0
mkdir build
cd build
cmake .. -DCMAKE_INSTALL_PREFIX=/usr
cmake --build .
sudo cmake --build . --target install
sudo ldconfig
```
Or the following for 16.04
```
git clone https://github.com/nanomsg/nanomsg
cd nanomsg
cmake .
make
sudo make install
sudo ldconfig
```

## Installing Bitcoind

Let us first install Bitcoind, because it takes some time to sync it all up (around 12 hours)

```
sudo add-apt-repository ppa:bitcoin/bitcoin
sudo apt-get update
sudo apt-get install bitcoind bitcoin-qt
```

Let's create folder .bitcoin
```
cd ~/
mkdir .bitcoin
cd .bitcoin
vim bitcoin.conf
```

Paste this in your bitcoin.conf (replace recuser and rpcpassword)
```
server=1
daemon=1
txindex=1
rpcuser=bitcoinrpc
rpcpassword=password
bind=127.0.0.1
rpcbind=127.0.0.1
```

secure the bitcoin.conf file
```
chmod 600 ~/.bitcoin/bitcoin.conf
```

Start Bitcoind
```
bitcoind &
```

check to see if it's downloading the blocks
```
bitcoin-cli getinfo
```

This takes some time depending your internet connection. Let it run in the background.
Now it is time to install Komodo. Follow each line step by step and ignore the "libgmp headers missing" at some point!

## Installing Komodo

```
cd ~
git clone https://github.com/KomodoPlatform/komodo
cd komodo
git checkout beta
./zcutil/fetch-params.sh
```

-j8 uses 8 threads - replace 8 with number of threads you want to use or nproc variable
```
./zcutil/build.sh -j$(nproc)
```
This can take some time.


## When it is finished, let's create komodo.conf
```
cd ~
mkdir .komodo
cd .komodo
vim komodo.conf
```

Add the following lines to the komodo.conf file (replace rpcuser and rpcpassword)
```
rpcuser=bitcoinrpc
rpcpassword=password
txindex=1
bind=127.0.0.1
rpcbind=127.0.0.1
```

Now let's start the mining process. Use CTRL-C to get out when you miss your cursor
```
cd ~
cd komodo
```

To start the daemon to import btcdwif later on this setup
```
./src/komodod -gen -genproclimit=2 &
```



### Komodo is mining right now.
Here are some additional commands which will be handy in the future

This will get the stats:
```
./src/komodo-cli getinfo
```

To stop the daemon:
```
./src/komodo-cli stop 
```

To view komodod output (very handy):
```
tail -f ~/.komodo/debug.log
```

To view all commands
```
./src/komodo-cli help
```

## Setting up KomodoPlatform/Iguana.
Personally I prefer to have a full downloaded Bitcoin blockchain. But be sure you stop Bitcoind when you are going to install KomodoPlatform/Iguana!!
```
cd ~
git clone https://github.com/KomodoPlatform/KomodoPlatform
cd KomodoPlatform/iguana
git checkout beta
./m_LP
```

Temporary: start `~/KomodoPlatform/agents/iguana` just for setup, not for normal NN use, ie one time to get the pubkey
```
~/KomodoPlatform/agents/iguana
```

Open up a new SSH window and login to your server
```
cd ~/KomodoPlatform/iguana/coins
./basilisk.old
```

if you receive this error: "ERROR BINDING PORT.8332 this is normal tcp timeout, unless another process is using port" then you were already running Bitcoind in the background. Stop Iguana and Bitcoind and start the above again.

Create an iguana wallet with encryptwallet and importprivkey into both komodod and bitcoind using the KMDwif and BTCwif in the encryptwallet result once you have them installed and running. Put in a really good password and be sure you keep the slash at the end.
```
curl --url "http://127.0.0.1:7778" --data "{\"agent\":\"bitcoinrpc\",\"method\":\"encryptwallet\",\"passphrase\":\"insert very secure password here\"}"
```

Goto to ~/KomodoPlatform/iguana and create the executable file "wp"
```
cd ~/KomodoPlatform/iguana
vim wp
```

Paste this into your wp file and be sure you set the password that you have made above (watch out for the slash at the end)
```
curl --url "http://127.0.0.1:7778" --data "{\"method\":\"walletpassphrase\",\"params\":[\"same passphrase as above\", 9999999]}"
```

Make it executable
```
chmod +x wp
```

Run the just made file
```
./wp
```

In the output of the executed file you will see a lot of data. Get the btcpubkey (not the pubkey!) and send it to Kolo. I advise you to copy the output and safe it somewhere. 

Create a text file `~/KomodoPlatform/iguana/userhome.txt` with just this path in it
```
vim ~/KomodoPlatform/iguana/userhome.txt
# and put your home folder in it. Mostly it is home/username (without the front and back slash!)
```

Copy these files then change them from using port 7778 to 7776
```
cp ~/KomodoPlatform/iguana/coins/btc ~/KomodoPlatform/iguana/
cp ~/KomodoPlatform/iguana/coins/kmd ~/KomodoPlatform/iguana/
cp ~/KomodoPlatform/iguana/wp ~/KomodoPlatform/iguana/wp_7776
```

Now create a new file for the pubkey. Enter it as:
pubkey=xxxxxxxxxxxxxxxxxxxxxxx
```
vim pubkey.txt
cp ~/KomodoPlatform/iguana/pubkey.txt ~/komodo/src/pubkey.txt
```

We have installed all the things we needed, but we have some configurations to do. Komodo is now mining with his own komodo pubkey, but we have to integrate some stuff into komodo. Let's hope you have copied and pasted the part where you got your btcpubkey etc. somewhere. Bring it back up.

## Final Steps
While komodo is still mining we can send commands to it. We need to import the privkey of your BTCD address into Komodo. Find your BTCDwif key (do NOT mistake it with your BTCwif). Now let's import it.
```
cd ~
cd komodo
./src/komodo-cli importprivkey BTCDwif
# replace BTCDwif with the key you received earlier (like: UvCbPGo2B5QHKgMN5KFRz10sMzbTSXunRTLB9utqGhNFUZrJrEWa)
```

To check to see if it imported successfully run
```
./src/komodo-cli validateaddress btcdaddress
# replace btcdaddress with the address you received earlier (like: RVxtoUT0CXbC1LrtltNAf9yR5yWnFnSPQh)
```

if `ismine: true` it has been successfully imported

We have successfully imported the BTCD address into Komodo. 

Now we have to integrate your BTC privkey into your Bitcoin installation. Be sure Bitcoind is running at this point!

Import BTC Priv Key (BTCwif)
```
bitcoin-cli importprivkey BTCwif &
# replace BTCwif with the key you received earlier (like: L3Qm5bB3frS2rdMNtmZrEMReRvYKMReALwxMaf00oz9YahvZaB4a)
```

Run the following to confirm it has imported properly.
```
bitcoin-cli validateaddress yourbtcaddress 
# replace yourbtcaddress with the address you received earlier (like: 1MghixZrbhncwLGTIiP3ZdeDKhzBaPUKKu)
```

if `ismine: true` it has been successfully imported

Now we need to chain everything together. Pondsea came up with a nice handy little script. So let's start

Create a script file at `/home/username/` and name it start
```
vim start
```

Paste into file and replace the pubkey with your btcpubkey and save it.
```
bitcoind &
cd komodo
./src/komodod -gen -genproclimit=2 -notary -pubkey="0225aa6f6f19e543180b31153d9e6d55d41bc7ec2ba191fd29f19a2f973544e29d" &
```

Make it executable
```
chmod +x start
```

This should bind all the tech stuff together, but not after we make some tweaking to the system. Hagbard came up with the following tweak.

# Set ulimit parameters on ubuntu permanent:
By default the number of open files pro user in Ubuntu is 1024. In our case this number is too small so you have to increase it.

This is done with the ulimit command:
```
$ulimit -a   # see all the kernel parameters
$ulimit -n   #see the number of open files
$ulimit -n 1000000  #  set the number open files to 1000000
```

The problem with this way is that the ulimit parameter is only set currently for this command terminal and user.
This means that after a reboot you’ll need to set the parameter again. Do following to set it permanent:

edit /etc/security/limits.conf
```
sudo vim /etc/security/limits.conf
```

add these lines:
```
* soft nofile 1000000
* hard nofile 1000000
```

save and close file

edit /etc/pam.d/common-session 
```
sudo vim /etc/pam.d/common-session
```

add this line:
```
session required pam_limits.so
```

save and close file

reboot & check:
```
ulimit -n
```

We are done. Now let's reboot the server and chain everything together with the start script and m_notary

When the server is rebooted and you are logged in as user (and lands into your home dir)
```
./start
CTRL-C
cd komodo/src
./assetchains (and wait 15 minutes before you go to the next step)
cd ~/KomodoPlatform/iguana
git checkout beta && git pull && ./m_notary && cd ~/komodo/src && ./dpowassets
```

We are done! If you have given the btcpubkey to James and he has added it to the notaries.h files (located https://github.com/KomodoPlatform/KomodoPlatform/blob/master/iguana/notaries.h) everything should work now.


# N00b Q&A

- I receive "null utxo array size" as output when i start script start2
	You have to fund your BTC wallet with 0.01btc. Send your BTC address to James and he will fund it.
	
- If I did not initially run bitcoind or komodod with txindex=1, then should I add that to .conf and rescan blockchain or something?
	Yes, you need to launch it with -reindex in the command line arg (like bitcoind -reindex &)

- When i start Iguana, i see a message that my IP is dead. Is it?
        It isn't ;-) just start de start2 script and it's alive.

- When i log out of my server and log back in again after i while, the Iguana proces has been killed, why?
        I do not know why, i had this problem myself. Contact BadAss for a solution for this.

- I get all kinds of strange warnings in the output of start2. Did i broke something?
        Rule nr.1, if you do not see any ERROR's, let it run. James is busy with debugging the code. Do you see warnings, then let it run. If you don't trust it, restart it again.

- My Iguana process gets killed by a buffer overflow error. Do i have to start over again?
        No, just go to ~/Supernet and do a git pull. 