Blacklist Addresses for Mempool Patch
======================================

* base on bitcoind version: `v0.12.1`

This patch will read addresses from the blacklist file and prevent txs which spend from these addresses. Only prevent txs get into mempool, if the tx has already got into block, it won't reject the block.

## Usage

### apply the patch

```
# download v0.12.1 source file
wget https://github.com/bitcoin/bitcoin/archive/v0.12.1.tar.gz
tar zxf v0.12.1.tar.gz
cd bitcoin-0.12.1

# copy patch to here
cp bitcoind-mempoolblacklist-v0.12.1.patch .

# apply patch
patch -p1 < bitcoind-mempoolblacklist-v0.12.1.patch

# build bitcoind
./autogen.sh
./configure --disable-wallet
make -j4

# if build success, there will be an option:
$ ./src/bitcoind --help | grep mempoolblacklistaddress
-mempoolblacklistaddress=<file>
```

### run bitcoind

First, you need write addresses into the text file, one line one address:

```
#
# vim /work/blacklist-address.txt
#
13vHWR3iLsHeYwT42RnuKYNBoVPrKKZgRv
3DeDa7eSSr8KdW24zJzCgdEA6XJgHzmKn3
```

Then, start bitcoind with option: `-mempoolblacklistaddress`, as below:

```
bitcoind -mempoolblacklistaddress=/work/blacklist-address.txt
```

If tx's input spend from one of the blacklist addresses, you'll see `debug.log` like this:

```
2016-08-05 22:45:17 load blacklist address size: 2

...

2016-08-05 23:11:37 ERROR: AcceptToMemoryPool: blacklist address found in tx: 28798c2805638910e24072b50ece36e47c13ae7580fea9b6b862b8432c5ac492, 13vHWR3iLsHeYwT42RnuKYNBoVPrKKZgRv
2016-08-05 23:12:34 ERROR: AcceptToMemoryPool: blacklist address found in tx: 568984eb70e8cb20421db1bbb6cef0ed31e5cdab612f9ddba0d59507973f333a, 13vHWR3iLsHeYwT42RnuKYNBoVPrKKZgRv
2016-08-05 23:12:48 ERROR: AcceptToMemoryPool: blacklist address found in tx: 9085e5edd635ccf7c167e98afabeb746763b94f0adeae08daff065dc78163bde, 13vHWR3iLsHeYwT42RnuKYNBoVPrKKZgRv
```