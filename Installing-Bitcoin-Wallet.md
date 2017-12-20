### Installing Bitcoin wallet on Ubuntu/Debian

The easiest way to install Bitcoin Wallet on your Linux machine is via repositories.

Let us first install Bitcoind, because it takes some time to sync it all up (around 12 hours)

```
sudo add-apt-repository ppa:bitcoin-unlimited/bu-ppa
sudo apt-get update
sudo apt-get install bitcoind bitcoin-qt
```

Let's create folder .bitcoin
```
cd ~/
mkdir .bitcoin
cd .bitcoin
nano bitcoin.conf
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

- Press CTRL+O to save the changes.
- Press CTRL+X to exit nano editor.


secure the bitcoin.conf file
```
chmod 600 ~/.bitcoin/bitcoin.conf
```

Start Bitcoind
```
bitcoind -daemon
```

check to see if it's downloading the blocks
```
bitcoin-cli getinfo
```