---
title: installAndHelp
date: 2017-04-17 22:53:16
categories: mongodb
tags: 
    - install
    - mongo
---

# 官网
当然，最权威的还是官网
[官方wiki](https://docs.mongodb.com)
# 安装
- [官网安装方式](https://docs.mongodb.com/manual/administration/install-enterprise/)
- 安装之后可以将mongodb的bin目录增加到PATH目录中
- 例如mac：只需要`brew install mongodb`,
然后将`/usr/local/Cellar/mongodb/3.4.2/bin`加入到path中即可
# 启动
- 创建目录
demo：
```linux
/chimps/web_back/mongodb ll
drwxr-xr-x   3 chimp  wheel  102  3 21 14:01 conf
drwxr-xr-x  20 chimp  wheel  680  3 21 14:32 data
drwxr-xr-x   4 chimp  wheel  136  3 21 14:07 log
```
- 增加conf文件
此时没有增加用户认证，生产环境肯定要加的
```
/chimps/web_back/mongodb cat conf/mongod.conf
port = 12345
dbpath = data
logpath = log/mongod.log
fork = true
```
- 运行`mongod -f /conf/mongod.conf`
之前将/usr/local/Cellar/mongodb/3.4.2/bin加入到PATH中，mongod可执行文件就在此目录下
- 日志存放在`log/mongod.log`中
```
/chimps/web_back/mongodb tail -f log/mongod.log
2017-03-21T14:07:03.850+0800 I -        [initandlisten] Detected data files in /chimps/web_back/mongodb/data created by the 'wiredTiger' storage engine, so setting the active storage engine to 'wiredTiger'.
... ...
```
#   连接
##   mongo -h
```shell
~ mongo -h
MongoDB shell version v3.4.2
usage: mongo [options] [db address] [file names (ending in .js)]    # 格式
db address can be:
  foo                   foo database on local machine
  192.168.0.5/foo       foo database on 192.168.0.5 machine
  192.168.0.5:9999/foo  foo database on 192.168.0.5 machine on port 9999
Options:
  --shell                             run the shell after executing files
  --nodb                              don't connect to mongod on startup - no
                                      'db address' arg expected
  --norc                              will not run the ".mongorc.js" file on
                                      start up
  --quiet                             be less chatty
  --port arg                          port to connect to
  --host arg                          server to connect to
  --eval arg                          evaluate javascript
  -h [ --help ]                       show this usage information
  --version                           show version information
  --verbose                           increase verbosity
  --ipv6                              enable IPv6 support (disabled by default)
  --disableJavaScriptJIT              disable the Javascript Just In Time
                                      compiler
  --disableJavaScriptProtection       allow automatic JavaScript function
                                      marshalling
  --ssl                               use SSL for all connections
  --sslCAFile arg                     Certificate Authority file for SSL
  --sslPEMKeyFile arg                 PEM certificate/key file for SSL
  --sslPEMKeyPassword arg             password for key in PEM file for SSL
  --sslCRLFile arg                    Certificate Revocation List file for SSL
  --sslAllowInvalidHostnames          allow connections to servers with
                                      non-matching hostnames
  --sslAllowInvalidCertificates       allow connections to servers with invalid
                                      certificates
  --sslFIPSMode                       activate FIPS 140-2 mode at startup
  --networkMessageCompressors arg     Comma-separated list of compressors to
                                      use for network messages
  --jsHeapLimitMB arg                 set the js scope's heap size limit

Authentication Options:
  -u [ --username ] arg               username for authentication
  -p [ --password ] arg               password for authentication
  --authenticationDatabase arg        user source (defaults to dbname)
  --authenticationMechanism arg       authentication mechanism
  --gssapiServiceName arg (=mongodb)  Service name to use when authenticating
                                      using GSSAPI/Kerberos
  --gssapiHostName arg                Remote host name to use for purpose of
                                      GSSAPI/Kerberos authentication

file names: a list of files to run. files have to end in .js and will exit after unless --shell is specified
```
- 只需要：`mongo localhost:12345`
# 数据库操作
- `db.help()`
```js
> db.help()
DB methods:
	db.adminCommand(nameOrDocument) - switches to 'admin' db, and runs command [ just calls db.runCommand(...) ]
	db.auth(username, password)
	db.cloneDatabase(fromhost)
	db.commandHelp(name) returns the help for the command
	db.copyDatabase(fromdb, todb, fromhost)
	db.createCollection(name, { size : ..., capped : ..., max : ... } )
	db.createView(name, viewOn, [ { $operator: {...}}, ... ], { viewOptions } )
	db.createUser(userDocument)
	db.currentOp() displays currently executing operations in the db
	db.dropDatabase()
	db.eval() - deprecated
	db.fsyncLock() flush data to disk and lock server for backups
	db.fsyncUnlock() unlocks server following a db.fsyncLock()
	db.getCollection(cname) same as db['cname'] or db.cname
	db.getCollectionInfos([filter]) - returns a list that contains the names and options of the db's collections
	db.getCollectionNames()
	db.getLastError() - just returns the err msg string
	db.getLastErrorObj() - return full status object
	db.getLogComponents()
	db.getMongo() get the server connection object
	db.getMongo().setSlaveOk() allow queries on a replication slave server
	db.getName()
	db.getPrevError()
	db.getProfilingLevel() - deprecated
	db.getProfilingStatus() - returns if profiling is on and slow threshold
	db.getReplicationInfo()
	db.getSiblingDB(name) get the db at the same server as this one
	db.getWriteConcern() - returns the write concern used for any operations on this db, inherited from server object if set
	db.hostInfo() get details about the server's host
	db.isMaster() check replica primary status
	db.killOp(opid) kills the current operation in the db
	db.listCommands() lists all the db commands
	db.loadServerScripts() loads all the scripts in db.system.js
	db.logout()
	db.printCollectionStats()
	db.printReplicationInfo()
	db.printShardingStatus()
	db.printSlaveReplicationInfo()
	db.dropUser(username)
	db.repairDatabase()
	db.resetError()
	db.runCommand(cmdObj) run a database command.  if cmdObj is a string, turns it into { cmdObj : 1 }
	db.serverStatus()
	db.setLogLevel(level,<component>)
	db.setProfilingLevel(level,<slowms>) 0=off 1=slow 2=all
	db.setWriteConcern( <write concern doc> ) - sets the write concern for writes to the db
	db.unsetWriteConcern( <write concern doc> ) - unsets the write concern for writes to the db
	db.setVerboseShell(flag) display extra information in shell output
	db.shutdownServer()
	db.stats()
	db.version() current version of the server
```
- collection 相关操作
假设我有个user 字段
```js
> db.user.help()
DBCollection help
	db.user.find().help() - show DBCursor help
	db.user.bulkWrite( operations, <optional params> ) - bulk execute write operations, optional parameters are: w, wtimeout, j
	db.user.count( query = {}, <optional params> ) - count the number of documents that matches the query, optional parameters are: limit, skip, hint, maxTimeMS
	db.user.copyTo(newColl) - duplicates collection by copying all documents to newColl; no indexes are copied.
	db.user.convertToCapped(maxBytes) - calls {convertToCapped:'user', size:maxBytes}} command
	db.user.createIndex(keypattern[,options])
	db.user.createIndexes([keypatterns], <options>)
	db.user.dataSize()
	db.user.deleteOne( filter, <optional params> ) - delete first matching document, optional parameters are: w, wtimeout, j
	db.user.deleteMany( filter, <optional params> ) - delete all matching documents, optional parameters are: w, wtimeout, j
	db.user.distinct( key, query, <optional params> ) - e.g. db.user.distinct( 'x' ), optional parameters are: maxTimeMS
	db.user.drop() drop the collection
	db.user.dropIndex(index) - e.g. db.user.dropIndex( "indexName" ) or db.user.dropIndex( { "indexKey" : 1 } )
	db.user.dropIndexes()
	db.user.ensureIndex(keypattern[,options]) - DEPRECATED, use createIndex() instead
	db.user.explain().help() - show explain help
	db.user.reIndex()
	db.user.find([query],[fields]) - query is an optional query filter. fields is optional set of fields to return.
	                                              e.g. db.user.find( {x:77} , {name:1, x:1} )
	db.user.find(...).count()
	db.user.find(...).limit(n)
	db.user.find(...).skip(n)
	db.user.find(...).sort(...)
	db.user.findOne([query], [fields], [options], [readConcern])
	db.user.findOneAndDelete( filter, <optional params> ) - delete first matching document, optional parameters are: projection, sort, maxTimeMS
	db.user.findOneAndReplace( filter, replacement, <optional params> ) - replace first matching document, optional parameters are: projection, sort, maxTimeMS, upsert, returnNewDocument
	db.user.findOneAndUpdate( filter, update, <optional params> ) - update first matching document, optional parameters are: projection, sort, maxTimeMS, upsert, returnNewDocument
	db.user.getDB() get DB object associated with collection
	db.user.getPlanCache() get query plan cache associated with collection
	db.user.getIndexes()
	db.user.group( { key : ..., initial: ..., reduce : ...[, cond: ...] } )
	db.user.insert(obj)
	db.user.insertOne( obj, <optional params> ) - insert a document, optional parameters are: w, wtimeout, j
	db.user.insertMany( [objects], <optional params> ) - insert multiple documents, optional parameters are: w, wtimeout, j
	db.user.mapReduce( mapFunction , reduceFunction , <optional params> )
	db.user.aggregate( [pipeline], <optional params> ) - performs an aggregation on a collection; returns a cursor
	db.user.remove(query)
	db.user.replaceOne( filter, replacement, <optional params> ) - replace the first matching document, optional parameters are: upsert, w, wtimeout, j
	db.user.renameCollection( newName , <dropTarget> ) renames the collection.
	db.user.runCommand( name , <options> ) runs a db command with the given name where the first param is the collection name
	db.user.save(obj)
	db.user.stats({scale: N, indexDetails: true/false, indexDetailsKey: <index key>, indexDetailsName: <index name>})
	db.user.storageSize() - includes free space allocated to this collection
	db.user.totalIndexSize() - size in bytes of all the indexes
	db.user.totalSize() - storage allocated for all data and indexes
	db.user.update( query, object[, upsert_bool, multi_bool] ) - instead of two flags, you can pass an object with fields: upsert, multi
	db.user.updateOne( filter, update, <optional params> ) - update the first matching document, optional parameters are: upsert, w, wtimeout, j
	db.user.updateMany( filter, update, <optional params> ) - update all matching documents, optional parameters are: upsert, w, wtimeout, j
	db.user.validate( <full> ) - SLOW
	db.user.getShardVersion() - only for use with sharding
	db.user.getShardDistribution() - prints statistics about data distribution in the cluster
	db.user.getSplitKeysForChunks( <maxChunkSize> ) - calculates split points over all chunks and returns splitter function
	db.user.getWriteConcern() - returns the write concern used for any operations on this collection, inherited from server/db if set
	db.user.setWriteConcern( <write concern doc> ) - sets the write concern for writes to the collection
	db.user.unsetWriteConcern( <write concern doc> ) - unsets the write concern for writes to the collection
	db.user.latencyStats() - display operation latency histograms for this collection
```
