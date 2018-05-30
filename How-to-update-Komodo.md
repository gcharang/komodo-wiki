Sometimes it is needed to update Komodo. This is how I did it.

Reboot your node (to start clean).

Go into the Komodo folder

```
cd komodo
make clean
git checkout beta
git pull
./zcutil/build.sh -j8
```

**If you need to resync from scratch follow the instructions below:**

Go to your `.komodo` folder, remove the following files and start `komodod`.

```
cd ~/.komodo
rm -rf blocks chainstate debug.log komodostate db.log
cd ~/komodo/src
./komodod &
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