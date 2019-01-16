# How to Encrypt Komodo wallet.dat with password to make it more secure?

Komodo supports `encryptwallet` to secure your wallet.dat file. After encrypting your wallet, you need to unlock it before making any transaction or dumping privkey of an address. This prevents unauthorized access to your KMD and other assetchains.

## Requirements:
- Native wallet daemon for KMD and/or any  `-ac_public` chains (SPV or Lite mode will not work)

## Precautions / Best Practices
- Don't encrypt any wallet with z address. Transfer your funds from a private z address to transparent R address first ([Guide](https://support.komodoplatform.com/en/support/solutions/articles/29000026955-perform-z-transactions-using-agama/preview)).
- Don't forget your your password. If you do, you will loose access to your funds.
- Use a strong password containing letters, numbers, UPPERCASE, lowercase, special characters.
- Always keep a backup or write down your password, passphrase and private keys in a safe place.
- Always back your wallet.dat in a safe place. Better to do it after sending transaction everytime.
- You are responsible for your funds, not the developers.

## Encrypt Wallet with Password

The `encryptowallet` command will encrypt your wallet.dat with a password that you provide.
**Usage:**
```
encryptwallet "password"
```
**Example:**
```
./komodo-cli encryptwallet Y0urSecureP@$$phras3
```
Issuing this command will return you the following output and shut down the daemon.
```
wallet encrypted; Komodo server stopping, restart to run with encrypted wallet. The keypool has been flushed, you need to make a new backup.
```
Now, start your native coin wallet again. Your `wallet.dat` is already encrypted. Most of the wallet features will now require you to unlock the wallet first before performing those actions. You will get output like below:
```
error code: -13
error message:
Error: Please enter the wallet passphrase with walletpassphrase first.
```
That means, without unlocking your wallet, you can't send funds or dump private key of any address that the wallet holds. Follow the next step for commands to unlock your wallet.

## Unlock Wallet
You need to use `walletpassphrase` command with your password and timeout time to unlock your wallet for sending funds or some other actions. Use the `timeout` option to set a timer for how many seconds the wallet will be unlocked before locking automatically.
**Usage:**
```
walletpassphrase "passphrase" timeout
```
**Example:**
```
./komodo-cli walletpassphrase Y0urSecureP@$$phras3 60
```
This above command will unlock your wallet for 60 seconds and will **NOT** return any output in your console. You can perform any actions with your wallet without any restrictions in the next 60 seconds. Change the `timeout` numbers to your liking.

## How to change password?

**Usage:**
```
walletpassphrasechange "oldpassphrase" "newpassphrase"
```
**Example:**
```
./komodo-cli walletpassphrasechange Y0urSecureP@$$phras3 YourNewSecur3Pa$$phr@se
```
After issuing this command, there will be no output in your teminal console. But, `debug.log` will print the following line:
```
2018-12-05 15:39:38 Wallet passphrase changed to an nDeriveIterations of 299405
```
This means your password has been changed successfully. Use the new password for unlocking your wallet from this time forward until you change it again.

**Always remember if you forget your password, you will not be able to access your funds. So, keep it safely backed up.**