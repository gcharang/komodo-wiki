### Installing Litecoin on Notary Node

```shell
cd ~
wget https://download.litecoin.org/litecoin-0.13.2/linux/litecoin-0.13.2-x86_64-linux-gnu.tar.gz
tar -zxvf litecoin-0.13.2-x86_64-linux-gnu.tar.gz
sudo install -m 0755 -o root -g root -t /usr/local/bin ~/litecoin-0.13.2/bin/*
rm -rf ~/litecoin-0.13.2/ litecoin-0.13.2-x86_64-linux-gnu.tar.gz
```

After above commands you must be able to use `litecoind` and `litecoin-cli` from anywhere in terminal.

Let's setup litecoin configuration:
```shell
cd ~
mkdir .litecoin
cd .litecoin
nano litecoin.conf
```

Paste this in your litecoin.conf (replace recuser and rpcpassword values)

```shell
rpcuser=litecoinrpc
rpcpassword=password
txindex=1
server=1
daemon=1
rpcbind=127.0.0.1
bind=127.0.0.1
```

- Press CTRL+O to save the changes.
- Press CTRL+X to exit nano editor.

Start Litecoind
```
litecoind
```

check to see if it's downloading the blocks
```
litecoin-cli getinfo
```