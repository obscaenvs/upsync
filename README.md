# `upsync`

An [`fswatch`](https://github.com/emcrisostomo/fswatch)-based `bash`-script that, based on a dot file `.upsync`
in a directory with the correct credentials, watches the directory for
changes and takes the specified upload action. This script is immature and essentially just a configurable wrapper for `fswatch`. 

# License
Licensed under the ["Simplified BSD license"](https://en.wikipedia.org/wiki/BSD_licenses#2-clause_license_.28.22Simplified_BSD_License.22_or_.22FreeBSD_License.22.29), which is FSF approved, so it should work well with the licensing terms of `fswatch`. Drop me a line if you disagree.

# Version details

This version of `upsync` works with `fswatch-1.3.9` and
`fswatch-1.4.0`, and since it doesn't use may features of it, probably
somewhat older versions. As of now, only tested on Mac OS X
10.8. Testing under way on FreeBSD 10.0-RELEASE.

# Bugs

Sometimes it's slow to react to a change. I have yet to investigate
why. I don't think it is the underlying program (i.e.`fswatch`)
Probably more, being written in `bash`. Let me know.

# Todo
  * Better error messages (e.g. when starting the script)
  * Document the script better (work in progress)

# Example
Let's say we have a directory 
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
and the contents of the .upsync file are:
```
remote_user:root
remote_machine:dev.example.com
remote_directory:/tmp/watched_dir
remote_method:rsync -vrP
local_ignore: .git ignored_dir
```
We launch the upsync command thusly

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


  