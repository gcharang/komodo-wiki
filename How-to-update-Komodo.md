Sometimes it is needed to update Komodo. This is how I did it.

Reboot your node (to start clean).

Go into the Komodo folder

```
cd komodo
```

Do a git pull

```
git pull
```

**ATTENTION: When it is not a new beta version you do make in the ~/komodo/src dir**

```
cd src
make
```

**ATTENTION: If it is a new beta version you forget above and do what is down under**

```
git checkout dPoW
```

The follow the following steps:

```
./zcutil/fetch-params.sh
```

```
./zcutil/build.sh -j8
```

After that, go to your .komodo folder

```
cd ~/.komodo
```

Remove the following files

```
rm -rf blocks chainstate debug.log komodostate db.log
```

Go back to you home folder

```
cd ~
```

Run your start script

```
./start
```

Let it resync (check with the getinfo command). When it is done resync, start Iguana

```
cd ~/KomodoPlatform/iguana
./m_notary
```

It has been done

================================
# Problems?

I receive the following error when i do "./zcutil/build.sh -j8"

```
EXCEPTION: St13runtime_error
could not load param file at /home/j/.zcash-params/sprout-verifying.key
Komodo in AppInit()
```
You have to do "./zcutil/fetch-params.sh" first and after that "./zcutil/build.sh -j8"