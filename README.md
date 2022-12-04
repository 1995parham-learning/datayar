# Datayar

## Introduction

Consider an environment based on k8s with two separate namespaces for production and testing.
In the production environment, you have an PostgreSQL database that you want its data
on your testing environment even you need to change its structure and fields.
*Datayar* is here to help you.

## Step by Step

First of all, we must export the production database:

```bash
oc project <production-namespace>

echo <database-password> | oc rsh -c <database-container> <database-pod> pg_dump -U <database-user> <database-name> > output.sql
```

You can use these exported data with the provided `docker-compose` to alter tables or remove redundant data.

e.g.

- Remove rows that have not-null `deleted_at`.

```sql
delete from <table> where deleted_at is not null;
```

In the end, we export `insert`-only SQL files and load them into the testing environment using `pgsl`.

```bash
oc project <testing-namespace>

echo <database-password> | oc rsh -c <database-container> <database-pod> psql -U <database-user> <database-name> -f - < <table-1>.sql
echo <database-password> | oc rsh -c <database-container> <database-pod> psql -U <database-user> <database-name> -f - < <table-2>.sql
echo <database-password> | oc rsh -c <database-container> <database-pod> psql -U <database-user> <database-name> -f - < <table-3>.sql
```

Also, you can do this on your system as follow:

```bash
pg_dump -U <database-user> -W -h 127.0.0.1 -d <database-name> -a -f output.sql
```

```bash
psql -U <database-user> -h 127.0.0.1 -f - < output.sql
```
