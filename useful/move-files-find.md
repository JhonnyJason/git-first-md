# Copy or Move Files with `find`

First of all a shoutout to  Eric P. Allman, David MacKenzie and all the people who contributed to `find` - a very powerful tool and part of [GNU](https://www.gnu.org/). As the name suggests it is used to find files and execute further commands on the found files.

Here we simply want to copy all non-hidden files from one directory to another. While we could use `cp /path/to/dir/* /path/to/other/` we run into problems, as soon as we have some directories. Same holds for the `mv` command.

This is why we use `find` as in this magic command:
```bash
find /path/to/dir/* -type f -maxdepth 0 -exec mv {} /path/to/other/ \;
```

If we want the hidden files as well the commands turns to this:
```bash
find /path/to/dir/ -type f -maxdepth 1 -exec mv {} /path/to/other/ \;
```

## Explaination

- `find` would always operate recursively from the provided path. If you choose the non-hidden files directly by using the glob `*` you can stay at depth 0 for each found file. If you also want to include the hidden files you can choose the parent directory instead with a depth of 1.
- `-type f` this is the selector for files.
- `-exec mv {} /path/to/other/ \;` executes the `mv {} /path/to/other/` command for every found file. the `{}` is the placeholder for the found file.

