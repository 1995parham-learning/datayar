# Datayar

## Introduction

Consider an environment based on k8s that you have two separate namespace for production and testing.
In the production environment you have an SQL database that you want its data on your testing environemnt even
you need to change their structure and fields. Datayar is here to help you.

## Step by Step

First of all, we must export the production database:

```sh
oc project <production-namespace>

echo "<database-password>" | oc rsh -c <database-container> <database-pod> pg_dump -U <database-user> <database-name> > output.sql
```

You can use these exported data with the provided `docker-compose` to alter tables or remove redundant data.
At the end, we export `insert`-only SQL files and load them into the testing environment.

e.g.

- Remove rows that have not-null `deleted_at`.

```sql
delete from <table> where deleted_at is not null;
```

```sh
oc project <testing-namespace>

echo "<database-password>" | oc rsh -c <database-container> <database-pod> psql -U <database-user> <database-name> -f - < <table-1>.sql
echo "<database-password>" | oc rsh -c <database-container> <database-pod> psql -U <database-user> <database-name> -f - < <table-2>.sql
echo "<database-password>" | oc rsh -c <database-container> <database-pod> psql -U <database-user> <database-name> -f - < <table-3>.sql
```
