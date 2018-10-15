spanner-cli
===

[![CircleCI](https://circleci.com/gh/yfuruyama/spanner-cli.svg?style=svg)](https://circleci.com/gh/yfuruyama/spanner-cli)

spanner-cli, the Cloud Spanner command line interface.

## Description

`spanner-cli` is a command line client for [Cloud Spanner](https://cloud.google.com/spanner/).  
You can control your Spanner databases with idiomatic SQL commands like `mysql(1)`.

This tool is still **ALPHA** quality.  
Do not use this tool for operating production databases.

## Install

```
go get -u github.com/yfuruyama/spanner-cli
```

This tool uses [Application Default Credentials](https://cloud.google.com/docs/authentication/production?hl=en#providing_credentials_to_your_application) as credential source to connect to Spanner databases.
Please be sure to prepare your credential by `gcloud auth application-default login`.

## Example

```
$ spanner-cli --project=<PROJECT> --instance=<INSTANCE> --database=<DATABASE>

Connected.
spanner> CREATE TABLE users (
      ->   id INT64 NOT NULL,
      ->   name STRING(16) NOT NULL,
      ->   active BOOL NOT NULL
      -> ) PRIMARY KEY (id);
Query OK, 0 rows affected (30.60 sec)

spanner> SHOW TABLES;
+-----------------+
| Tables_in_my_db |
+-----------------+
| users           |
+-----------------+
1 rows in set (18.66 msecs)

spanner> INSERT INTO users (id, name, active) VALUES (1, "foo", true), (2, "bar", false);
Query OK, 2 rows affected (5.08 sec)

spanner> SELECT * FROM users ORDER BY id ASC;
+----+------+--------+
| id | name | active |
+----+------+--------+
| 1  | foo  | true   |
| 2  | bar  | false  |
+----+------+--------+
2 rows in set (3.09 msecs)

spanner> BEGIN;
Query OK, 0 rows affected (0.02 sec)

spanner(rw txn)> DELETE FROM users WHERE active = false;
Query OK, 1 rows affected (0.61 sec)

spanner(rw txn)> COMMIT;
Query OK, 0 rows affected (0.20 sec)

spanner> SELECT * FROM users ORDER BY id ASC;
+----+------+--------+
| id | name | active |
+----+------+--------+
| 1  | foo  | true   |
+----+------+--------+
1 rows in set (2.58 msecs)

spanner> DROP TABLE users;
Query OK, 0 rows affected (25.20 sec)

spanner> SHOW TABLES;
Empty set (2.02 msecs)

spanner> EXIT;
Bye
```

## Syntax

The syntax is case-insensitive.

| Usage | Syntax | Note |
| --- | --- | --- |
| List databases | `SHOW DATABASES;` | |
| Switch database | `USE <database>;` | |
| Create database | `CREATE DATABSE <database>;` | |
| Drop database |  | Not supported yet |
| List tables | `SHOW TABLES;` | |
| Show table schema | `SHOW CREATE TABLE <table>;` | |
| Create table | `CREATE TABLE ...;` | |
| Change table schema | `ALTER TABLE ...;` | |
| Delete table | `DROP TABLE ...;` | |
| Create index | `CREATE INDEX ...;` | |
| Delete index | `DROP INDEX ...;` | |
| Query | `SELECT ...;` | |
| DML | `INSERT / UPDATE / DELETE ...;` | |
| Partitioned DML | | Not supported yet |
| Start Read-Write Transaction | `BEGIN (RW);` | |
| Commit Read-Write Transaction | `COMMIT;` | |
| Rollback Read-Write Transaction | `ROLLBACK;` | |
| Start Read-Only Transaction | `BEGIN RO;` | |
| End Read-Only Transaction | `CLOSE;` | |
| Show Query Execution Plan | | Not supported yet |

## How to develop

Run unit tests

```
$ make test
```

Run unit tests and integration tests, which connects to real Cloud Spanner database.

```
$ SPANNER_CLI_INTEGRATION_TEST_PROJECT_ID=$PROJECT_ID SPANNER_CLI_INTEGRATION_TEST_INSTANCE_ID=${INSTANCE_ID} SPANNER_CLI_INTEGRATION_TEST_DATABASE_ID=${DATABASE_ID} SPANNER_CLI_INTEGRATION_TEST_CREDENTIAL=${CREDENTIALS} make test
```

## TODO

* STRUCT data type
* comment literal (#)
* prompt customize
* vertical format for query result (\G)
* EXPLAIN
* DESCRIBE
* evaluate SQL from stdin
* show secondary index by "SHOW CREATE TABLE"
