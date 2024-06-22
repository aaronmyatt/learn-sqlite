# Sqlite Archive
From the docs: https://sqlite.org/sqlar.html

```ts
import $ from 'jsr:@david/dax';
import {assert} from 'jsr:@std/assert'
```

## .archive / .ar

```text
.archive ...             Manage SQL archives
   Each command must have exactly one of the following options:
     -c, --create               Create a new archive
     -u, --update               Add or update files with changed mtime
     -i, --insert               Like -u but always add even if unchanged
     -r, --remove               Remove files from archive
     -t, --list                 List contents of archive
     -x, --extract              Extract files from archive
   Optional arguments:
     -v, --verbose              Print each filename as it is processed
     -f FILE, --file FILE       Use archive FILE (default is current db)
     -a FILE, --append FILE     Open FILE using the apndvfs VFS
     -C DIR, --directory DIR    Read/extract files from directory DIR
     -g, --glob                 Use glob matching for names in archive
     -n, --dryrun               Show the SQL that would have occurred
   Examples:
     .ar -cf ARCHIVE foo bar  # Create ARCHIVE from files foo and bar
     .ar -tf ARCHIVE          # List members of ARCHIVE
     .ar -xvf ARCHIVE         # Verbosely extract files from ARCHIVE
   See also:
      http://sqlite.org/cli.html#sqlite_archive_support
```

## archive the entire $HOME/Downloads directory
```ts
$.logStep('archive $HOME/Downloads')
await $`sqlite3 -A -cf $HOME/Downloads.sqlar -C $HOME/Downloads/`
const gotArchive = await Deno.stat(`${Deno.env.get('HOME')}/Downloads.sqlar`)
assert(gotArchive)
```