# Datayar

## Introduction

Consider an environment based on k8s that you have two separate namespace for production and testing.
In the production environment you have an SQL database that you want its data on your testing environemnt even
you need to change their structure and fields. Datayar is here to help you.

## Step by Step

First of all, we must export the production database:

```sh
# Switch context to the production environemnt

oc project <production-namespace>

# Please note that the following command required password and you need to type it on the fly because all outputs are redirected

oc rsh -c <database-container> <database-pod> pg_dump -U <database-user> <database-name> > output.sql
```

As mentioed before the export file contains `Password:` prompt in the first line so remove it before any usage.
You can use these exported data with the provided `docker-compose` to alter tables or remove redundant data.
At the end, we export `insert`-only SQL files and load them into the testing environment.

e.g.

- Remove rows that have not-null `deleted_at`.

```sql
delete from <table> where deleted_at is not null;
```
- Remove rows that have not-null `deleted_at`.

```sql
delete from <table> where deleted_at is not null;
```

```sh
# Switch context to the testing environemnt

oc project <testing-namespace>

# Please note that the following command required password and you need to type in the first line of each export file because all inputs are redirected

oc rsh -c <database-container> <database-pod> psql -U <database-user> <database-name> -f - < <table-1>.sql
oc rsh -c <database-container> <database-pod> psql -U <database-user> <database-name> -f - < <table-2>.sql
oc rsh -c <database-container> <database-pod> psql -U <database-user> <database-name> -f - < <table-3>.sql
```
