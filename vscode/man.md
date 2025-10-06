# VScode

### Installation

Prefer installation with `apt-get` rather than with `snap`:

```bash
sudo apt-get update
sudo apt-get install software-properties-common apt-transport-https -y
sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main" > /etc/apt/sources.list.d/vscode.list'
sudo apt-get update
sudo apt-get install code
```


### Handy features

```bash
code . # open VSCode in current directory
```


ctrl+P (cmd+P) --- search for file.
ctrl+click / alt+'double click' --- open file to the side
ctrl+G --- go to line (negative is also possible)

find in list (explorer, outline): https://stackoverflow.com/questions/31639563/how-to-filter-files-shown-in-vs-codes-explorer-view
`/` --- find file in explorer

`breadcrumbs`

### Dump

- `settings.json`

- `code --list-extensions | xargs -L 1 echo code --install-extension > ext.txt`

