# nanopy
Python 3 implementation of NANO-related functions and an RPC light wallet.

Addresses can be input in both `xrb_` and `nano_`. However, the RPC responses from the nodes are still in `xrb_` format.

## Wallet options
* The wallet looks for default configuration in `$HOME/.config/nanopy-wallet.conf`.
  * Default mode of operation is to check state of all accounts in `$HOME/.config/nanopy-wallet.conf`.
* `--new`. Generate a new seed and derive index 0 account from it.
  * Seeds are generated using `os.urandom()`
  * Generated seeds are stored in a GnuPG AES256 encrypted file.
  * AES256 encryption key is 8 bytes salt + password stretched with 65011712 rounds of SHA512.
  * Wallets can also be made by encrypting a file that has a seed using the command, `gpg -ca --s2k-digest-algo SHA512 FILE`
  * Options used by the encryption can be verified by inspecting the header in the gpg file. `gpg --list-packets --verbose FILE.asc`. `cipher 9` is AES256. `s2k 3` is iterated and salted key derivation mode. `hash 10` corresponds to SHA512. `count` is the number of iterations (max 65011712).
  * To get the seed, `gpg -d FILE.asc`
* `--audit-file`. Check state of all accounts in a file.
* `--broadcast`. Broadcast a block in JSON format. Blocks generated on an air-gapped system using `--offline` tag can be broadcast using this option.
* `-t` or `--tor`. Communicate with RPC node via the tor network.

The wallet has a sub-command, `nanopy-wallet open FILE.asc`, to unlock previously encrypted seeds. `open` has the following options.
* `-i` or `--index`. Index of the account unlocked from the seed. (Default=0)
* `-s` or `--send-to`. Supply destination address to create a send block.
  * Send amount is rounded off to 6 decimal places.
* `--empty-to`. Empty out funds to the specified send address.
* `--unlock`. Unlock wallet.
* `-c` or `--change-rep-to`. Supply representative address to change representative.
  * Change representative tag can be combined with send and receive blocks.
* `--remote`. Compute work on the RPC node.
  * Work generation is local by default. If the C library is compiled, that is used. Otherwise, the python function is used.
* `--audit`. Check state of all accounts from index 0 to the specified limit. (limit is supplied using the `-i` tag)
* `--offline`. Generate blocks in offline mode. In the offline mode, current state of the account is acquired from the default configuration in `$HOME/.config/nanopy-wallet.conf`. Refer to the sample file for more details.
* `--demo`. Run in demo mode. Never broadcast blocks.

## C library for work generation
  * For CPU blake2 libraries are required. `sudo apt-get install libb2-dev`
  * For GPU, appropriate OpenCL ICD and headers are required. `sudo apt-get install ocl-icd-opencl-dev`
