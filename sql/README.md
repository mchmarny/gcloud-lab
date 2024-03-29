# sql

This sample automates the process of setting up a Cloud SQL instance with secure (TLS) access.

> Note, to keep this readme short, I will be asking you to execute scripts rather than listing here complete commands. You should really review each one of these scripts for content, and, to understand the individual commands so you can use them in the future.

## Pre-requirements

If you don't have one already, start by creating new project and configuring [Google Cloud SDK](https://cloud.google.com/sdk/docs/).

## Instance

The [bin/instance](bin/instance) script will:

* Create a Cloud SQL instance
* Set the default (root) user credentials
* Configure MySQL database in the new Cloud SQL instance
* Set up application database user and its credentials
* Create and download client SSL certificates from the newly created instance

> Note, while the created Cloud SQL instance will be exposed to the world (`0.0.0.0/0`), it allow only SSL connections. Also, the root and app user passwords created in first step. If you ever decide to remove the SSL connection requirements, you can reset the root password in the Cloud SQL UI.

```shell
bin/instance
```

## Schema

The [bin/schema](bin/schema) script applies database schema located in [ddl/schema.sql](ddl/schema.sql).

> Ideally, the provided script checks for existence of all the objects before creating them so you can run it multiple times.

```shell
bin/schema
```

## Connection

At this point you should be able to connect to the newly created database with this command:

```shell
bin/connect
```

> Use `\q` to exit.

## Disclaimer

This is my personal project and it does not represent my employer. I take no responsibility for issues caused by this code. I do my best to ensure that everything works, but if something goes wrong, my apologies is all you will get.
