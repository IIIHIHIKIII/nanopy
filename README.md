# nanopy
Install by running `pip install nanopy`.

## Usage
* Functions in the core library are written in the same template as [nano's RPC protocol](https://github.com/nanocurrency/nano-node/wiki/RPC-protocol). If there is a function, you can more or less call it the same way you would get that `action` done via RPC. For e.g., the RPC `action` to generate work for a hash is, [work_generate](https://github.com/nanocurrency/nano-node/wiki/RPC-protocol#work-generate) with `hash` as a parameter. In the library, the `action` becomes the function name and parameters become function arguments. Thus to generate work, call `work_generate(hash)`.
  * Optional RPC parameters become optional function arguments in python. In `work_generate`, `use_peers` and `difficulty` are optional arguments available for RPC. However, `use_peers` is not a useful argument for local operations. Thus only `difficulty` is available as an argument. It can be supplied as `work_generate(hash, difficulty=x)`.
  * Only purely local `action`s are supported in the core library (work generation, signing, account key derivations, etc.).
* Functions in the `rpc` sub-module follow the exact template as [nano's RPC protocol](https://github.com/nanocurrency/nano-node/wiki/RPC-protocol). Unlike the core library, there is no reason to omit an `action` or parameter. Thus the library is a fully compatible API to nano-node's RPC.
* [nano's RPC wiki](https://github.com/nanocurrency/nano-node/wiki/RPC-protocol) can be used as a manual for this library. There are no changes in `action` or `parameter` names, except in a few cases \(`hash`, `id`, `type`\) where the parameter names are keywords in python. For those exceptions, arguments are prepended with an underscore \(`_hash`, `_id`, `_type`\).

## Building from source
* Point to a custom compiler (default is `gcc`) by prepending the installation command with `CC=path/to/custom/c/compiler`.
  * When using `Visual C`, additionally prepend the installation command with `USE_VC=1`.
* For GPU, appropriate OpenCL ICD and headers are required. `sudo apt-get install ocl-icd-opencl-dev nvidia-opencl-icd/amd-opencl-icd`
  * Enable GPU usage by prepending the installation command with `USE_GPU=1`.

## Wallet
Although not part of the package, the light wallet included in the repository is a good reference to understand how the library works.

### Wallet options
* The wallet looks for default configuration in `$HOME/.config/nanopy/<network>.conf`.
  * `<network>` is one of nano, banano, or beta. nano is the default.
  * Default mode of operation is to check state of all accounts in `$HOME/.config/nanopy/<network>.conf`.
* `--new`. Generate a new seed and derive index 0 account from it.
  * Seeds are generated using `os.urandom()`
  * Generated seeds are stored in a GnuPG AES256 encrypted file.
  * AES256 encryption key is 8 bytes salt + password stretched with 65011712 rounds of SHA512.
  * Wallets can also be made by encrypting a file that has a seed using the command, `gpg -ca --s2k-digest-algo SHA512 FILE`
  * Options used by the encryption can be verified by inspecting the header in the gpg file. `gpg --list-packets --verbose FILE.asc`. `cipher 9` is AES256. `s2k 3` is iterated and salted key derivation mode. `hash 10` corresponds to SHA512. `count` is the number of iterations (max 65011712).
  * To get the seed, `gpg -d FILE.asc`
* `--audit-file`. Check state of all accounts in a file.
* `--broadcast`. Broadcast a block in JSON format. Blocks generated on an air-gapped system using `--offline` tag can be broadcast using this option.
* `--network`. Choose the network to interact with - nano, banano, or beta. The default network is nano.
* `-t` or `--tor`. Communicate with RPC node via the tor network.

The wallet has a sub-command, `nanopy-wallet open FILE.asc`, to unlock previously encrypted seeds. `open` has the following options.
* `-i` or `--index`. Index of the account unlocked from the seed. (Default=0)
* `-s` or `--send-to`. Supply destination address to create a send block.
* `--empty-to`. Empty out funds to the specified send address.
* `--unlock`. Unlock wallet.
* `-c` or `--change-rep-to`. Supply representative address to change representative.
  * Change representative tag can be combined with send and receive blocks.
* `--audit`. Check state of all accounts from index 0 to the specified limit. (limit is supplied using the `-i` tag)
* `--offline`. Generate blocks in offline mode. In the offline mode, current state of the account is acquired from the default configuration in `$HOME/.config/nanopy/<network>.conf`. Refer to the sample file for more details.
* `--demo`. Run in demo mode. Never broadcast blocks.

## Support
Contact me on [reddit](https://www.reddit.com/user/nanopy_). You can support the project by reporting any bugs you find and/or submitting fixes/improvements. When submitting pull requests please format the code using `yapf` (for Python) or `clang-format` (for C).
```
clang-format --style google -i nanopy/*.c
yapf --style google -i -r nanopy nanopy-wallet setup.py
```
Donations: `nano_3ooycog5ejbce9x7nmm5aueui18d1kpnd74gc4s67nid114c5bp4g9nowusy`.
