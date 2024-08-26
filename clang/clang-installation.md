# Clang installation

## Основная информация

Официальный сайт: [apt.llvm.org](https://apt.llvm.org/) (section `Automatic installation script`)


## Установка

Добавить [Ubuntu toolchain repository](../ubuntu-toolchain-repository.md)

### Скрипт (предпочтительно)

Вместо `symlink` лучше использовать `update-alternatives`.

Полный гайд.

```bash
CLANG_VERSION=18

cd ./clang
curl -O https://apt.llvm.org/llvm.sh # or wget
chmod +x llvm.sh
sudo ./llvm.sh -${CLANG_VERSION}
sudo apt-get autoremove
sudo update-alternatives --install /usr/bin/clang clang /usr/bin/clang-${CLANG_VERSION} ${CLANG_VERSION} \
  --slave /usr/bin/clang++ clang++ /usr/bin/clang++-${CLANG_VERSION}
sudo update-alternatives --display clang
```

* Если хотим уметь отдельно переключать версию компилятора `C` и `C++`:

```bash
sudo update-alternatives --install /usr/bin/clang clang /usr/bin/clang-${CLANG_VERSION} ${CLANG_VERSION}
sudo update-alternatives --display clang
sudo update-alternatives --install /usr/bin/clang++ clang++ /usr/bin/clang++-${CLANG_VERSION} ${CLANG_VERSION}
sudo update-alternatives --display clang++
```

* `symlink` вместо `update-alternatives`

```bash
sudo ln -s /usr/bin/clang-${CLANG_VERSION} /usr/bin/clang
sudo ln -s /usr/bin/clang++-${CLANG_VERSION} /usr/bin/clang++
```

### Скачивание бинарников

<https://releases.llvm.org/>

### Ручная

```bash
CLANG_VERSION=18
LLVM_KEYRING_PATH="/usr/share/keyrings/llvm-archive-keyring.gpg"
LLVM_SOURCES_LIST_PATH="/etc/apt/sources.list.d/llvm-clang-$CLANG_VERSION.list"

wget -O - https://apt.llvm.org/llvm-snapshot.gpg.key 2>/dev/null | gpg --dearmor - > "${LLVM_KEYRING_PATH}"

if [ ! -f "${LLVM_KEYRING_PATH}" ]; then
  echo "deb [signed-by=${LLVM_KEYRING_PATH}] http://apt.llvm.org/jammy/ llvm-toolchain-jammy-${CLANG_VERSION} main" > "${LLVM_SOURCES_LIST_PATH}"
  echo "deb-src [signed-by=${LLVM_KEYRING_PATH}] http://apt.llvm.org/jammy/ llvm-toolchain-jammy-${CLANG_VERSION} main" >> "${LLVM_SOURCES_LIST_PATH}"
fi

apt-get update
```

## Замечания

* `apt-key add` is deprecated: [stackoverflow](https://stackoverflow.com/questions/68992799/warning-apt-key-is-deprecated-manage-keyring-files-in-trusted-gpg-d-instead). Только устанавливать ключ нужно в `/usr/share/keyring`: смотри [cmake kitware-archive.sh](https://apt.kitware.com/kitware-archive.sh) в конце скрипта.


## Команды компилятора

### Стандарт

```bash
clang++ -std=c++17 test.cpp
```

### Библиотека

```bash
clang++ -stdlib=libc++ test.cpp # native for Mac OS X
clang++ -stdlib=libstdc++ test.cpp # native for GNU/Linux
```
