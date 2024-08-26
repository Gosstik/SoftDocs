# GCC installation

## Полный гайд

[phoenixnap.com](https://phoenixnap.com/kb/install-gcc-ubuntu)

## Основная информация

* Релизы: [gcc.gnu.org/releases.html](https://gcc.gnu.org/releases.html).

## Установка через Universe (PPA) repository

Добавить [Ubuntu toolchain repository](../ubuntu-toolchain-repository.md)

Полный гайд:

```bash
GCC_VERSION=13

sudo apt-get install -y gcc-${GCC_VERSION} g++-${GCC_VERSION}
sudo apt-get autoremove -y
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-${GCC_VERSION} ${GCC_VERSION} \
  --slave /usr/bin/g++ g++ /usr/bin/g++-${GCC_VERSION} \
  --slave /usr/bin/gcov gcov /usr/bin/gcov-${GCC_VERSION}
sudo update-alternatives --display gcc
```

Если хотим уметь отдельно переключать версию компилятора `C` и `C++`:

```bash
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-${GCC_VERSION} ${GCC_VERSION}
sudo update-alternatives --display gcc
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-${GCC_VERSION} ${GCC_VERSION}
sudo update-alternatives --display g++
```

Альтернатива `update-alternatives` --- `symlink` (но лучше использовать `update-alternatives`)

```bash
sudo ln -s /usr/bin/gcc-${CLANG_VERSION} /usr/bin/gcc
sudo ln -s /usr/bin/g++-${CLANG_VERSION} /usr/bin/g++
```

## Установка из исходников (для самых последних версий)

Отрывок кода позаимствован из [этого вопроса](https://askubuntu.com/a/1518433).

Найти свежие версии можно в [Index of /gnu/gcc](https://ftp.gnu.org/gnu/gcc/).

```bash
GCC_VERSION=14.1.0

sudo apt-get install build-essential
sudo apt-get install libmpfr-dev libgmp3-dev libmpc-dev -y
wget http://ftp.gnu.org/gnu/gcc/gcc-${GCC_VERSION}/gcc-${GCC_VERSION}.tar.gz
tar -xf gcc-${GCC_VERSION}.tar.gz
cd gcc-${GCC_VERSION}
./configure -v --build=x86_64-linux-gnu --host=x86_64-linux-gnu --target=x86_64-linux-gnu --prefix=/usr/local/gcc-${GCC_VERSION} --enable-checking=release --enable-languages=c,c++ --disable-multilib --program-suffix=-${GCC_VERSION}
make
sudo make install
```
