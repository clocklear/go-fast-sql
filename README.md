# go-batch-insert
A Golang library to make batch inserts easy.

##Example usage

```go
package main

import (
	"database/sql"
	_ "github.com/go-sql-driver/mysql"
	"github.com/rmulley/go-batch-insert"
	"log"
	"net/url"
) //import

func main() {
	var (
		err error
		i   uint = 1
		dbh *sql.DB
		bi  *batchinsert.BatchInsert_t
	) //var

	// Set up DB conn using Go's built-in database/sql pkg and go-sql-driver's MySQL driver
	if dbh, err = sql.Open("mysql", "user:pass@tcp(localhost:3306)/db_name?"+url.QueryEscape("charset=utf8mb4,utf8&loc=America/New_York")); err != nil {
		log.Fatalln(err)
	} //if
	defer dbh.Close()

	// Create new BatchInsert object
	bi = batchinsert.NewBatchInsert(dbh, 100)

	// Some loop performing SQL INSERTs
	for i <= 250 {
		if err = bi.Insert("INSERT INTO test_table(id, id2, id3) VALUES(?, ?, ?);", i, i + 1, i + 2); err != nil {
			log.Fatalln(err)
		} //if

		i++
	} //for

	// Flush out remaining insert (Last 50 rows)
	if err = bi.Flush(); err != nil {
		log.Fatalln(err)
	} //if
} //main
```
