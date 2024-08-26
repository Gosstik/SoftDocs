# update-alternatives

* Базовый синтаксис: [baeldung](https://www.baeldung.com/linux/update-alternatives-command)
* Полный ман по `update-alternatives`: [link](https://man7.org/linux/man-pages/man1/update-alternatives.1.html)

### Ubuntu repositories

Four types: [askubuntu.com](https://askubuntu.com/questions/58364/whats-the-difference-between-multiverse-universe-restricted-and-main)


### Show all alternatives available in the system

```bash
sudo update-alternatives --get-selections
```


### Перечислить доступные alternatives

```bash
sudo update-alternatives --list <name>
# sudo update-alternatives --list gcc
```


### Показать доступные alternatives

```bash
sudo update-alternatives --display <name>
# sudo update-alternatives --display java
```


### Поменять используемый alternatives

```bash
sudo update-alternatives --config <name>
# sudo update-alternatives --config editor
```


### Использовать alternatives по умолчанию (это происходит и у slaves)

* `По умолчанию` --- тот, у которого самый высокий priority

```bash
sudo update-alternatives --auto <name>
# sudo update-alternatives --config editor
```


### Добавить новый alternatives

* Ссылка с объяснением: [link](https://documentation.suse.com/ru-ru/sles/15-SP3/html/SLES-all/cha-update-alternative.html#sec-ua-install)

```bash
sudo update-alternatives --install <link> <name> <path> <priority> [--slave <link> <name> <path>]...
# sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-13 13 \ 
# --slave /usr/bin/g++ g++ /usr/bin/g++-13
```

* Slave позволяет при переключении `master` на альтернативу переключать и всех `slaves`.
