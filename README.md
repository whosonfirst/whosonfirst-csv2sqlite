# whosonfirst-csv2sqlite

A simple tool for indexing one or more CSV files in a SQLite database.

This tool _should_ work with any old CSV file but has a few [Who's On First](https://whosonfirst.mapzen.com) -isms baked in.
 
## Usage

```
csv2sqlite <DATABASE> <TABLE> <COMMA-SEPARATED-KEYS-TO-INDEX> <ONE-OR-MORE-CSV-FILES>
```

By default columns named `wof:id` or `id` are stored as integer primary keys. For example:

```
csv2sqlite concordances-admin.db concordances '' /usr/local/mapzen/whosonfirst-data/meta/wof-concordances-latest.csv 
```

See the way we're passing an empty `''` string for indexes. That's not awesome, but oh well. To index `wof:id` and `gp:id` and `gn:id` you would do:

```
csv2sqlite concordances-admin.db concordances 'gp:id,gn:id' /usr/local/mapzen/whosonfirst-data/meta/wof-concordances-latest.csv 
```

See the way we're passing keys with colons? Those colons will make SQLite sad so the code tries to take care of replacing them with underbars. `wof:id` becomes `wof_id` and so on.

## Example

```
$> csv2sqlite admin.db whosonfirst '' /usr/local/data/whosonfirst-data/meta/wof-country-latest.csv /usr/local/data/whosonfirst-data/meta/wof-region-latest.csv 
bbox TEXT,cessation TEXT,country_id TEXT,deprecated TEXT,file_hash TEXT,fullname TEXT,geom_hash TEXT,geom_latitude TEXT,geom_longitude TEXT,id TEXT,inception TEXT,iso TEXT,iso_country TEXT,lastmodified TEXT,lbl_latitude TEXT,lbl_longitude TEXT,locality_id TEXT,name TEXT,parent_id TEXT,path TEXT,placetype TEXT,region_id TEXT,source TEXT,superseded_by TEXT,supersedes TEXT,wof_country TEXT
# import /usr/local/data/whosonfirst-data/meta/wof-country-latest.csv with 220 /usr/local/data/whosonfirst-data/meta/wof-country-latest.csv records
# import /usr/local/data/whosonfirst-data/meta/wof-region-latest.csv with 4700 /usr/local/data/whosonfirst-data/meta/wof-region-latest.csv records
$> sqlite3 admin.db 
SQLite version 3.8.2 2013-12-06 14:53:30
Enter ".help" for instructions
Enter SQL statements terminated with a ";"
sqlite> SELECT COUNT(id) FROM whosonfirst WHERE wof_country="CA";
14
```

## Caveats

* CSV column names with colons will be replace with SQLite column names with underbars
* CSV column names starting with a number will make SQLite sad
* There is very little error-checking or error-handling. For example, if you are indexing multiple CSV files it is expected that they will all have the same headers in the same order

## See also

* https://whosonfirst.mapzen.com/
* https://www.sqlite.org/