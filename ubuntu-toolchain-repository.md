# Ubuntu toolchain repository

* `PPA` ---  Personal Package Archive.
* more about `ppa:ubuntu-toolchain-r/test` [link](https://launchpad.net/%7Eubuntu-toolchain-r/+archive/ubuntu/test).

```bash
sudo apt-get install -y software-properties-common
sudo add-apt-repository -y ppa:ubuntu-toolchain-r/test
sudo apt-get update
```

* `ppa:ubuntu-toolchain-r/test` can be added to your system manually by copying the lines below and adding them to your system's software sources (create file with custom name, e.g. `ubuntu-toolchain-r-ubuntu-test-jammy.list`, in folder `/etc/apt/sources.list.d`).

```bash
deb https://ppa.launchpadcontent.net/ubuntu-toolchain-r/test/ubuntu YOUR_UBUNTU_VERSION_HERE main 
# deb-src https://ppa.launchpadcontent.net/ubuntu-toolchain-r/test/ubuntu YOUR_UBUNTU_VERSION_HERE main 
```

## Проверить версии программ

```bash
sudo apt-cache search gcc
```
