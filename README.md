### Using Verible with FuseSoC

#### Prerequisites

Install prerequisites (tested on Ubuntu 18.04):
```bash
sudo apt update
sudo apt install cmake ninja-build wget python3 python3-pip python3-setuptools make tar git
sudo pip3 install fusesoc
```

#### Building Verible

Verible can be built using the [Bazel build system](https://bazel.build/).
Bazel is not available in the debian/ubuntu apt repositories. To install Bazel you should add Bazel's apt repository:

```bash
curl https://bazel.build/bazel-release.pub.gpg | sudo apt-key add -
echo "deb [arch=amd64] https://storage.googleapis.com/bazel-apt stable jdk1.8" | sudo tee /etc/apt/sources.list.d/bazel.list
sudo apt update && sudo apt install bazel
```

To build Verible, a C++11 compatible compiler is required. After the installation of bazel, Verible can be built and installed:

```bash
git clone https://github.com/google/Verible
cd Verible
bazel build -c opt //...
bazel run :install -c opt -- `pwd`/../Verible_bin
export PATH=`pwd`/../Verible_bin/:$PATH
```

Alternatively, you can download pre-built Verible binaries from the [Verible release page](https://github.com/google/Verible/releases). Refer to the [Verible documentation](https://github.com/google/Verible) and [bazel install](https://docs.bazel.build/versions/master/install.html) for more details.

#### Running Verible tools with FuseSoC

FuseSoC uses tool backends available in [edalize](https://github.com/olofk/edalize), which is another workflow automation project from the same author that we contribute to regularly. In order to use the Verible tools with FuseSoC you need to define a tool section in the FuseSoC `core` target file. Ibex already includes the integration, so you can use its [ibex_core.core](https://github.com/lowRISC/ibex/blob/master/ibex_core.core) file as an example.

#### Veriblelint with FuseSoC on ibex

To perform `Veriblelint` on `ibex_core`, use:

```bash
git clone https://github.com/lowRISC/ibex
cd ibex
#add Veriblelint rules
sed -i '132i\          - "-generate-label"\n          - "-unpacked-dimensions-range-ordering"\n          - "-explicit-parameter-storage-type"\n          - "-line-length"\n          - "-module-filename"\n          - "-no-trailing-spaces"\n          - "-undersized-binary-literal"\n          - "-struct-union-name-style"\n          - "-case-missing-default"\n          - "-explicit-task-lifetime"\n          - "-explicit-function-lifetime"' ibex_core.core

fusesoc --cores-root . run --target=lint --tool=veriblelint lowrisc:ibex:ibex_core:0.1
```

[![asciicast](https://asciinema.org/a/JpEj0cjtfmlZK53hlI0SpKX9O.svg)](https://asciinema.org/a/JpEj0cjtfmlZK53hlI0SpKX9O)

#### Veribleformat with FuseSoC on ibex

To perform a `Veribleformat` on `ibex_core`:

```bash
#add Veribleformat rules
sed -i '154i\          - "--max_search_states"\n          - "10000000"' ibex_core.core
fusesoc --cores-root . run --target=format --no-export lowrisc:ibex:ibex_core:0.1
```

[![asciicast](https://asciinema.org/a/CPN8pfrepDzGGbhaSc2BPyfKc.svg)](https://asciinema.org/a/CPN8pfrepDzGGbhaSc2BPyfKc)

