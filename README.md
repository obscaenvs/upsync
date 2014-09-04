# `upsync`

An [`fswatch`](https://github.com/emcrisostomo/fswatch)-based `bash`-script that, based on a dot file `.upsync`
in a directory with the correct credentials, watches the directory for
changes and takes the specified upload action.

# Version
This version of `upsync` works with `fswatch-1.3.9` and `fswatch-1.4.0`, and since it doesn't use may features of it, probably somewhat older versions.

# Example
Given a directory 
```
/tmp/watched_dir/
├── .git
│   └── some_git_file
├── .upsync
├── ignored_dir
│   └── source_file_3.src
├── just_a_dir
│   └── source_file_2.src
└── source_file.src
```
and the contents of the .upsync file being
```
remote_user:root
remote_machine:dev.example.com
remote_directory:/tmp/watched_dir
remote_method:rsync -vrP
local_ignore: .git ignored_dir
```
we launch the upsync command thusly 

```
$ upsync watched_dir
```

This will watch any file not inside a directory listed in the
`local_ignore`-configuration variable and send it to the remote server
by issuing, under the supposition that the file
`/tmp/watched_dir/just_a_dir/source_file_2.src` was the one being
updated, the command

```
$ rsync -vrP /tmp/watched_dir/just_a_dir/source_file_2.src root@dev.example.com:/tmp/watched_dir/just_a_dir/source_file_2.src
```
Example output:
```
$ upsync watched_dir/
++ Uploading file: /Users/fcar/tmp/watched_dir/just_a_dir/source_file_2.src
   rsync -vrP /Users/fcar/tmp/watched_dir/just_a_dir/source_file_2.src root@dev.example.com:/tmp/watched_dir/just_a_dir/source_file_2.src
sending incremental file list
source_file_2.src
             12 100%    0.00kB/s    0:00:00 (xfr#1, to-chk=0/1)

sent 95 bytes  received 41 bytes  90.67 bytes/sec
total size is 12  speedup is 0.09
```