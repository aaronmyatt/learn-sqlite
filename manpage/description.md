# Manpage: Description

```ts
import $ from 'jsr:@david/dax';
```

For example, to create a new database file named "mydata.db", create a table named "memos" and insert a couple of records into that table:

```ts
const query = [
    "CREATE TABLE memos(text, priority INTEGER)", 
    "INSERT INTO memos VALUES('Finish DAX tutorial', 10)", 
    "INSERT INTO memos VALUES('Learn more about DAX', 100)", 
    "SELECT * FROM memos"
]
console.log(await $`sqlite3 :memory: ${query.join(";")};`);
```