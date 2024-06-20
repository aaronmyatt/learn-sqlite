# Sqlite Meta-Commands

```ts
import $ from 'jsr:@david/dax';
$.logStep('Sqlite Meta-Commands')
```

## -A ARGS
I can see archiving sqlite being a powerful way of sharing state across machines on a network, maybe I could use this to create my personal Dropbox like backup across all devices. 

[x] research use cases and examples of archiving sqlite
[] create a simple example of archiving sqlite
[] what are the benefits of the archive format? Smaller? Portable? Secure?

```ts
$.logStep('-A ARGS')
$p.set(input, '/archive', await $`sqlite3 :memory: -A`.text('combined'));
```

Running `-A` with no args, like the clever dummy I am, reveals the following:

```text
Wrong number of arguments.  Usage:
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

Very handy. 

## -append
Once again, not entirely sure what value this provides, no doubt there are use cases, just need to find 'em. For instance, are we appending the sqlite file to another sqlite file? Or are we appending the output of a query to a file? Questions!

[] research use cases for -append

```ts
$.logStep('-append..?')
//console.log(await $`sqlite3 :memory: -append`);
```

## Output modes

### Setup test db
```ts
await $`sqlite3 test.db "CREATE TABLE IF NOT EXISTS memos(text, priority INTEGER)"`

const query = [
    "INSERT INTO memos VALUES('Finish DAX tutorial', 10)", 
    "INSERT INTO memos VALUES('Learn more about DAX', 100)"
]

const result = await $`sqlite3 -json test.db "SELECT COUNT(*) as count FROM memos"`.json()

const noData = $p.get(result, '/0/count') === 0
noData && await $`sqlite3 test.db ${query.join(";")}`;
```

### loopAndPrint
```ts
const modes = ["ascii", "box", "json", "markdown", "column", "html", "line", "csv", "list", "table", "tabs"]
for (const mode of modes) {
    $.logStep("-"+mode)
    const stdout = await $`sqlite3 -${mode} test.db "SELECT * FROM memos"`.text()
    $p.set(input, '/modes/'+mode, stdout);
    console.log(stdout)
}
```

## -bail
I'm guessing this is useful for testing?

[] research use cases for -bail
```ts
$.logStep('-bail')
```