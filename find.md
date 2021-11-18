# Find

Find is a unix program with a cli to find files and directories on your system.

General schema:

```bash
find <Directory> <Option> <Pattern>
```

## Search for files

For example, to search for all `.txt` files in the current directory, use the period symbol `.`.
Ususally the search is done recursively, so all subfolders get searched as well.
Having no `-type` flag, means it is looking for files, which is equals to the `-type f` flag.

```bash
find . -name *.txt
find . -type f -name *txt
```

To search through the whole root directory, you must use `sudo`.

```bash
sudo find / -name rc.lua
```

You can also search for case insensitive terms with the `-iname` flag.

```bash
find -type f -iname 'Linux'
```

## Search for directories

To search for a directory, use the `-type d` flag.
Keep in mind, that folders, that start with a period must be especially specified.

```bash
sudo find / -type d -name awesome
```

You can also use wildcard characters, like `*` to find files.

```bash
sudo find / -name '*awesome*'
```

## Search by size

You can search files by size or in a range of sizes with the `-size` flag.

```bash
find . -size +500k -size -10M
```

## Seach by permission

You can also serach for files by their permisson.

```bash
find -type f -perm 0777
```

With an exclamation mark `!` you can invert the specification.
So, for exampe, this searches for all files which do not have the permission set to `0777`.

```bash
find -type f ! -perm 0777
```
