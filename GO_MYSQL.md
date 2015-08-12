GOLANG MYSQL处理注意事项
=====

1.sql.Open返回连接池,不需要在每个db操作的时候调用sql.Open

```
// Open opens a database specified by its database driver name and a
// driver-specific data source name, usually consisting of at least a
// database name and connection information.
//
// Most users will open a database via a driver-specific connection
// helper function that returns a *DB. No database drivers are included
// in the Go standard library. See http://golang.org/s/sqldrivers for
// a list of third-party drivers.
//
// Open may just validate its arguments without creating a connection
// to the database. To verify that the data source name is valid, call
// Ping.
//
// The returned DB is safe for concurrent use by multiple goroutines
// and maintains its own pool of idle connections. Thus, the Open
// function should be called just once. It is rarely necessary to
// close a DB.
```

2.mysql释放连接问题

[问题阐述](http://my.oschina.net/waknow/blog/205654)

3.db.Prepare不释放