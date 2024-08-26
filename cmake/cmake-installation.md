# Cmake

## Install (simple way, removes old cmake)

Installation on ubuntu with `apt-add-repository`: [link](https://apt.kitware.com/) (see `kitware-archive.sh` for details).

Short instruction:

```bash
cd ./cmake
curl -O https://apt.kitware.com/kitware-archive.sh # or wget
chmod +x kitware-archive.sh
sudo ./kitware-archive.sh
sudo apt-get update
sudo apt-get install cmake
```

In case cmake version doesn't change --- delete cmake and install once again.


## Install (harder and flexible way, update-alternatives)

Alternative (much more complex, but more flexible) way - to download `tar` archive from [cmake.org/files](https://cmake.org/files/) to `/opt/cmake/cmake-<version>`, extract and add to `update-alternatives` or make symlink:

* `update-alternatives`

```bash
CMAKE_VERSION=3.30.2

sudo update-alternatives --install /usr/bin/cmake cmake /usr/bin/cmake/cmake-${CMAKE_VERSION} ${CMAKE_VERSION//./}
sudo update-alternatives --display cmake
sudo update-alternatives --config cmake # change default alternative
```

* `symlink`

```bash
ln -s /opt/cmake/cmake-<version>/bin/cmake /usr/bin/cmake
```
