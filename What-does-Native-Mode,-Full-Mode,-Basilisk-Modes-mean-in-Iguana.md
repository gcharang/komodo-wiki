### Starting PAX FIAT in *Native* mode with iguana

* Start komodod PAXFIAT

```shell
./komodod -ac_name=USD
```

* Start iguana `passthru` command connect to PAX USD via iguana

```shell
curl --url "http://127.0.0.1:7778" --data "{\"agent\":\"passthru\",\"method\":\"paxfiats\",\"mask\":1}"
```

### Starting PAX FIAT in *Basilisk* mode with iguana
NOTE: No need to start any coin or daemon locally. It'll connect to remote decentralized Full nodes/full native nodes in KomodoPlatform network

* Start iguana `passthru` command connect to PAX USD via iguana

```shell
curl --url "http://127.0.0.1:7778" --data "{\"agent\":\"basilisk\",\"method\":\"paxfiats\",\"mask\":1}"
```


### Starting PAX FIAT in *Iguana*'s Full mode with iguana
NOTE: This will not require any external `komodod` daemon to be running along side `iguana`.
It will start that PAX FIAT via `iguana` daemon itself in Full mode, and sync it's chain data with iguana (Memory Mapped Bundled blockchain files!)

* Start iguana `passthru` command connect to PAX USD via iguana

```shell
curl --url "http://127.0.0.1:7778" --data "{\"agent\":\"iguana\",\"method\":\"paxfiats\",\"mask\":1}"
```