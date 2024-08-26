# Vim

## Installation

1) Install dependencies

   ```bash
   sudo apt-get install -y clang vim curl clangd
   ```

2) Copy `.vimrc` to your home directory.

3) Install `vim-plug` ([vim-plug github README](https://github.com/junegunn/vim-plug?tab=readme-ov-file#unix)):

   ```bash
   curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
   ```

4) Install Vundle ([Vundle github README](https://github.com/VundleVim/Vundle.vim/blob/master/README.md#quick-start)):

   ```bash
   git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim
   ```

5) Install YouCompleteMe ([github installation guide](https://github.com/ycm-core/YouCompleteMe/blob/master/README.md#linux-64-bit)):

   ```bash
   sudo apt-get install -y build-essential git cmake vim-nox python3-dev
   mkdir -p ~/.vim/bundle/YouCompleteMe
   cd ~/.vim/bundle/YouCompleteMe
   git clone git@github.com:ycm-core/YouCompleteMe.git .
   git submodule update --init --recursive

   ### Add following to install.py for different languages support or use --all
   ### C#:                    --cs-completer
   ### Go:                    --go-completer
   ### JavaScript/TypeScript: --ts-completer (+ install npm and Node.js 18+)
   ### Rust:                  --rust-completer
   ### Java:                  --java-completer
   python3 install.py --clangd-completer
   #python3 install.py --all
   ```

6) Launch vim and run `:PluginInstall`

7) Choose and download icons pack from plugin `ryanoasis/vim-devicons`.

## Usage

- `:NerdTree` или `ctrl + n` &mdash; открыть встроенный проводник
