# Sqlite CLI Options

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
const modes = ["ascii", "box", "json", "markdown", "column", "html", "line", "csv", "list", "table", "tabs", "quote"]
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

## -batch
The website and manpage summary seem to emphasise Sqlite's prowess in batch processing:

> sqlite3 can also be used within shell scripts and other applications to provide batch processing features
> $ man sqlite3

I am keen to learn where one could offload to sqlite3 for bulk parallel processing.

[] research use cases for -batch
```ts
```

## -cmd
I'm guessing this is a way to run a command on startup? Maybe fill stdin with some output and pump it into sqlite3? Can I pair this with https://core.pipedown.dev?

[] research use cases for -cmd
```ts
```

## -echo
Seems quite useful, it outputs the SQL commands that are being run. Could be useful for studying, creating turorials, or debugging?

```ts
$.logStep('-echo')
const stdout = await $`sqlite3 -echo test.db "SELECT * FROM memos"`.text()
$p.set(input, '/modes/echo', stdout);
console.log(stdout)
```

## -init
Start up sqlite3 with a script file. Likely a good way to share a common setup script across multiple sqlite3 instances/machines/developers. We can also add a global init script in a shared location that all sqlite3 instances will read when starting up: `$HOME/sqlite3/sqliterc` or `~/.sqliterc`

## (Probably) above my pay grade
There are numerous additional options to start the sqlite3 cli with, but I suspect they are for more niche and advanced use cases, like the `-deserialize` option. I learned from the docs that sqlite3 serialises data by default as its default [multi threading strategy](https://sqlite.org/threadsafe.html), but whether that is related to the `-deserialize` option, I'm not sure. The goal here is to start weaving together knowledge!

I'll list through the remaining options and attempt to express them in my own words and come back with more accurate descriptions later.

- `-deserialize`
  - This option will read an existing sqlite file immediately into memory. Potentially a memory hogging operation if the sqlite file is large. Could this be a good way to speed up queries when opening multiple sqlite instances? Or maybe setup some sort of preconfigured sqlite instance for caching?
  - - `-maxsize`: The maximum size of the database file to deserialize.
- `-noheader`
  - controls whether to display column names in the output. Useful for controlling table and csv outputs.
- `-hexkey`
  - `-key`
  - `-textkey`
  - Looks like we get a few options for encrypting and opening encrypted sqlite files. Could I create an sqlite3 file per user and encrypt the whole thing?
- `-lookaside`
  - When I open a page like this, [Dynamic Memory Allocation in SQLite](https://sqlite.org/malloc.html), my brain readies itself not to understand. However, a consistent sensation I am getting from the SQLite documenation is that the authors have gone to great lengths to help us (lowly self-taught peons) understand! 
  - So I am equiped with a basic understanding that `-lookaside` is a configuration option that we can use to control how much memory an sqlite3 instance blocks out on startup. We can tweak this setting to optimise perfomance for specific workloads.
  - `-memtrace`: trace all memory allocations and deallocations
- `-nofollow`
  - Prevents the cli from opening symbolically linked files. Useful for security and preventing infinite loops? (👈 says Copilot 🤷)
- `-nonce`
  - Some way to bypass `-safe` restrictions. Needs to be paired with `.nonce` within an `sqlite3` script.
- `-nullvalue`
  - Use something other than an empty string for NULL values in the output. Can we do this per table? Or only global?
- `-readonly`
  - I can see this being handy to prevent accidental writes to a database. I imagine using it for pulling in databases that are essentially namespaces to different project/datasets and allowing us to pull in data about those projects without the risk of accidentally writing to them.
- `-safe`
  - "Safely" run a foreign script.
- `-vfs`
  - Reading the first few paragraphs about [SQLite VFS layer](https://sqlite.org/vfs.html) tells me that this options is useful to tailor which operating system SQLite should ready itself for. I don't suppose it'll be that commonly used!
- `-zip`
  - There seems to be a distinction between SQLite's archive format and either reading an SQLite file that has been zippped or simply reading an arbitrary zip files contents into an SQLite db. So, yeah, lots to dig into here!
- `-separator`
  - To granularly control what character separates column values, incase the many output modes provides are insufficient!