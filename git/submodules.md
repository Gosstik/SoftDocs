# Git submodules

Git submodules docs: [git-scm.com](https://git-scm.com/book/ru/v2/%D0%98%D0%BD%D1%81%D1%82%D1%80%D1%83%D0%BC%D0%B5%D0%BD%D1%82%D1%8B-Git-%D0%9F%D0%BE%D0%B4%D0%BC%D0%BE%D0%B4%D1%83%D0%BB%D0%B8).

### Adding submodule

```bash
git add submodule <ssh_url> <folder_name>
```

### Install project with submodule

```bash
# Option 1
git clone --recurse-submodules <ssh_of_current_project>

# Option 2
git clone <ssh_of_current_project>
git submodule init
git submodule update
```

### Pull changes

```bash
# Option 1 (auto)
git pull --recurse-submodules

# Option 2 (after git pull)
git submodule update --init --recursive --merge
```

### Update submodules

```bash
# Option 1 (auto)
# --merge - merge current changes
# --rebase - ...
git submodule update --remote --merge [<submodule_name>]

# Option 4 (manual)
cd <submodule>
git fetch
git merge
```

### Publish submodule changes

```bash
# Set config
git config push.recurseSubmodules check

# Option 1 (push with submodules)
git push --recurse-submodules=on-demand

# Option 2 (check that submodules are pushed)
git push --recurse-submodules=check
```

### Set specific branch to track

```bash
git config -f .gitmodules submodule.<submodule_name>.branch stable
git config status.submodulesummary 1
```
