# Postgrator CLI

[![Build Status][build-badge]][build]
[![npm package][npm-badge]][npm]
[![Coverage Status][coveralls-badge]][coveralls]
[![Dependency Status][dependency-status-badge]][dependency-status]
[![devDependency Status][dev-dependency-status-badge]][dev-dependency-status]

Command line SQL database migration tool using SQL scripts. For PostgreSQL, MySQL and SQL Server.

Version control your SQL database using plain old SQL files.

Supports also undoing migrations.

Uses [Postgrator](https://github.com/rickbergfalk/postgrator) node.js library developed by [Rick Bergfalk](https://github.com/rickbergfalk).

## Installation

*As of postgrator-cli 5 Node.js version 12 or greater is required*

```
npm install -g postgrator-cli
```

Or if you prefer to use it locally on your project using npm scripts of package.json:

```
npm install postgrator-cli --save-dev
```

And install the appropriate DB engine(s) if not installed yet:

```
npm install pg@8
npm install mysql@2
npm install mssql@6
```

See the [Postgrator](https://github.com/rickbergfalk/postgrator) documentation for more information about the supported engines.

## Usage

### SQL Files

Create a folder and stick some SQL scripts in there that change your database in some way. It might look like:

```
migrations/
  |- 001.do.sql
  |- 001.undo.sql
  |- 002.do.optional-description-of-script.sql
  |- 002.undo.optional-description-of-script.sql
  |- 003.do.sql
  |- 003.undo.sql
  |- 004.do.js
  |- 004.undo.js
  |- ... and so on
```

The files must follow the convention
[version].[action].[optional-description].sql or
[version].[action].[optional-description].js

**Version** must be a number, but you may start and increment the numbers in any
way you'd like. If you choose to use a purely sequential numbering scheme
instead of something based off a timestamp, you will find it helpful to start
with 000s or some large number for file organization purposes.

**Action** must be either "do" or "undo". Do implements the version, and undo
undoes it.

**Optional-description** can be a label or tag to help keep track of what
happens inside the script. Descriptions should not contain periods.

**SQL or JS** You have a choice of either using a plain SQL file or you can also
generate your SQL via a javascript module. The javascript module should export a
function called generateSql() that returns back a string representing the SQL.
For example:

```js
module.exports.generateSql = function () {
  return (
    "CREATE USER transaction_user WITH PASSWORD '" +
    process.env.TRANSACTION_USER_PASSWORD +
    "'"
  )
}
```

You might want to choose the JS file approach, in order to make use (secret)
environment variables such as the above.

### The tool

You can specify all the parameters from command line (see below) but the easiest way is to:

* Create `.postgratorrc.json`, or any config file supported by [cosmiconfig](https://github.com/davidtheclark/cosmiconfig). For example:

```
{
    "migrationPattern": "migrations/*",
    "driver": "pg",
    "host": "127.0.0.1",
    "port": 5432,
    "database": "myDatabaseName",
    "username": "user",
    "password": "pass"
}
```

* Migrate to latest version (it looks settings by default from `.postgratorrc.json`, etc):
```
$ postgrator
```

* Migrate to version 004 (it knows current version and migrates up/down automatically):
```
$ postgrator 4
```


### Synopsis

```
postgrator [[--to=]<version>] --database=<db> [--driver=<driver>] [--host=<host>] [--port=<port>] [--username=<username>] [--password=<password>] [--no-config]
```

### Options

```
  --to version                          Version number of the file to migrate to or 'max'. Default: 'max'
  -r, --driver pg|mysql|mssql           Database driver. Default: 'pg'.
  -h, --host hostname                   Host.
  -o, --port port                       Port.
  -d, --database database               Database name.
  -u, --username database               Username.
  -p, --password password               Password. If parameter without value is given, password will be asked.
  -m, --migration-pattern pattern       A pattern matching files to run migration files from. Default: 'migrations/*'
  -t --schema-table                     Table created to track schema version.
  --validate-checksum                   Validates checksum of existing SQL migration files already run prior to executing migrations.
  -s, --ssl                             Enables ssl connections. When using the mysql driver it expects a string containing name of ssl profile.
  -c, --config                          Explicitly set the location of the config file to load.
  --no-config                           Do not load options from a configuration file.
  -v, --version                         Print version.
  -?, --help                            Print this usage guide.

Examples

  1. Specify parameters on command line                       postgrator 23 --host 127.0.0.1 --database sampledb
                                                              --username testuser --password testpassword
  2. Explicitly disable loading configuration file            postgrator 2 --no-config
  3. Use default configuration file to migrate to version 5   postgrator 5
  4. Migrate to latest version using default configuration    postgrator
  file (.postgratorrc.json, etc)
```

## Tests
To run postgrator tests locally, run `docker-compose up` and then `npm test`.

[build-badge]: https://img.shields.io/github/workflow/status/MattiLehtinen/postgrator-cli/test/master?style=flat-square
[build]: https://github.com/MattiLehtinen/postgrator-cli/actions

[npm-badge]: https://img.shields.io/npm/v/postgrator-cli.svg?style=flat-square
[npm]: https://www.npmjs.org/package/postgrator-cli

[coveralls-badge]: https://img.shields.io/coveralls/MattiLehtinen/postgrator-cli/master.svg?style=flat-square
[coveralls]: https://coveralls.io/r/MattiLehtinen/postgrator-cli

[dependency-status-badge]: https://david-dm.org/MattiLehtinen/postgrator-cli.svg?style=flat-square
[dependency-status]: https://david-dm.org/MattiLehtinen/postgrator-cli

[dev-dependency-status-badge]: https://david-dm.org/MattiLehtinen/postgrator-cli/dev-status.svg?style=flat-square
[dev-dependency-status]: https://david-dm.org/MattiLehtinen/postgrator-cli#info=devDependencies
