# banano-vanity

Generate a banano address with a prefix of your choice.
The longer the prefix, the longer it'll take to compute.

## Compiling

First, setup Rust. The best way to do this is with [rustup](https://rustup.rs).

The next step is to clone the github repository. This can be done with

```bash
git clone https://github.com/flammenderdrache/banano-vanity.git
```

Alternatively you can also download the source code via the github website.

Next you have to cd into the cloned repository.

After that execute 

```bash
cargo build --release
```

You will find the finished binary in the `target/release` folder.

If you want to enable GPU support via OpenCL, you have to instead execute

```bash
cargo build --release --features gpu
```

You will find the binary in the same place as the one built without the GPU feature.

On Windows the build might fail because you are lacking a library needed to compile for OpenCL, it is called
`OpenCL.lib`. The easiest way to compile with it, is to just throw it into the `target/release/deps` folder,
then executing the build command again.

For a list of `banano-vanity` options, use `banano-vanity --help`.

## Seed Generation

(Although it is a bit slower i recommend generating seeds)

By default, `banano-vanity` generates private keys instead of seeds.
You can use these in the desktop wallet (they're referred to as adhoc keys),
however, most other wallets do not yet support them.

You can generate seeds instead of private keys with `--generate-seed`.
Note that doing so is a bit slower.

To explain the difference between seeds and private keys:

- Seeds plus an index (1st key, 2nd key, etc) generate a private key.
  Currently, this project will always use the first index (index 0).
- A private key generates a public key.
- Addresses are another way of writing public keys.

## Wildcards

You can leave a character up to chance by using `.` or `*`.

You can specify that a character must be a number with `#`.

## Using your GPU

This project supports using your GPU to compute the address.
This utilizes OpenCL, so you'll need OpenCL installed and working.

To build this project with GPU support, pass cargo `--features gpu`.

To enable GPU use, use the `--gpu` (or `-g`) option. To disable
use of your CPU, use `--threads 0` (or `-t 0`).

Intel GPUs are not supported, as in most cases running the code on
the integrated GPU is no faster than running it on the CPU.

To change your GPU device, use `--gpu-device [index]`, where `[index]`
is the index of your GPU starting at 0.
To change your GPU platform, use `--gpu-platform [index]`.

## Testing randomness

To test the randomness of seeds from this program, you can use dieharder
([here](http://www.linux-mag.com/id/4125/)'s an article on it).

Dieharder should not be taken as proof that this program is secure, however, it should be used as evidence, in combination
with an examination of the program's source code.

Here's an example of how to run this with dieharder:

```bash
banano-vanity --threads 1 --no-progress --limit 0 --simple-output nano_1 | cut -d' ' -f1 | xxd -r -p | dieharder -a -g stdin_input_raw
```

If you get a weak or failed test, run that test again by passing dieharder `-d [test]`.
While it's statistically unlikely that a test would fail despite nothing being wrong, it can happen,
especially given the number of tests dieharder runs.

To be even more careful, you can modify nano-vanity's parameters.
The important ones are `--simple-output`, which makes the output format easily parseable,
and `-l 0`, which generates infinite keys instead of just one.
