# BASE Migrations
*New Migration Tool*

GitHub Documentation: https://github.com/seppevs/migrate-mongo/blob/master/README.md
<br>
npm Documentation: https://www.npmjs.com/package/migrate-mongo

<blockquote>migrate-mongo is a database migration tool for MongoDB running in Node.js</blockquote> 

##### For local environment apply of migrations
- To create a new migration file run command (without " "):
```shell
migrate-mongo create "migration_fileName"
```

## CLI Usage for Reference
````
$ migrate-mongo
Usage: migrate-mongo [options] [command]

  Commands:

    create [description]  create a new database migration with the provided description
    up [options]          run all unapplied database migrations
    down [options]        undo the last applied database migration
    status [options]      print the changelog of the database

  Options:

    -h, --help     output usage information
    -V, --version  output the version number
    -f, --file     specify customized config file.
````


## Examples: 

### Creating Migrations
To create a migration, execute:

 ```shell
 migrate-mongo create <<name_of_migration>>
 ``` 

``migrate-mongo`` will create a node module within ``./migrations/`` which contains the following two exports:

 - The ``db`` object contains [the official MongoDB db object](https://www.npmjs.com/package/mongodb).

 - The ``client`` object is a [MongoClient](https://mongodb.github.io/node-mongodb-native/3.3/api/MongoClient.html) instance (which you can omit if you don't use it - in general it is not needed).

```js
module.exports = {
  async up(db, client) {
    // TODO write your migration here.
    // See https://github.com/seppevs/migrate-mongo/#creating-a-new-migration-script
    // Example:
    // await db.collection('albums').updateOne({artist: 'The Beatles'}, {$set: {blacklisted: true}});
  },

  async down(db, client) {
    // TODO write the statements to rollback your migration (if possible)
     // The client object is a MongoClient instance (which you can omit if you don’t use it - in general it is not needed).
    // Example:
    // await db.collection('albums').updateOne({artist: 'The Beatles'}, {$set: {blacklisted: false}});
  }
};
```

### Migrate up
This command will apply all pending migrations: 
```shell
$  migrate-mongo up
MIGRATED UP: 20160608155948-blacklist_the_beatles.js
```

If an an error occurred, it will stop and won't continue with the rest of the pending migrations.

If we check the status again, we can see the last migration was successfully applied:
```shell
$ migrate-mongo status
┌─────────────────────────────────────────┬──────────────────────────┐
│ Filename                                │ Applied At               │
├─────────────────────────────────────────┼──────────────────────────┤
│ 20160608155948-blacklist_the_beatles.js │ 2016-06-08T20:13:30.415Z │
└─────────────────────────────────────────┴──────────────────────────┘
```

### Migrate down
With this command, migrate-mongo will revert (only) the last applied migration:

```shell
$ migrate-mongo down
MIGRATED DOWN: 20160608155948-blacklist_the_beatles.js
```

If we check the status again, we see that the reverted migration is pending again:
```shell
$ migrate-mongo status
┌─────────────────────────────────────────┬────────────┐
│ Filename                                │ Applied At │
├─────────────────────────────────────────┼────────────┤
│ 20160608155948-blacklist_the_beatles.js │ PENDING    │
└─────────────────────────────────────────┴────────────┘
```
