# mantap-bank

## Installation lists:

- Go
- Docker
- TablePlus
- golang-migrate (`brew install golang-migrate`)

## Database structure definition

It's already self-documented on `mantap-bank-db-diagram.txt`. All you need to do is login into dbdiagram.io to export the database structure to Postgres.

## Go configuration

Install Go, and make sure the installation with this scripts:
```
go version && go env GOPATH
```

Use your text editor (vi, nano, emacs, etc.) and write into `~/.bash_profile`. In this example, I used vi:
```
vi ~/.bash_profile
```

And insert this line:
```
export PATH=$PATH:$(go env GOPATH)/bin
```
Save into that file.

And run this:
```
source ~/.bash_profile
```

## Postgres Configuration (with Docker)

Pull the image:
```
docker pull postgres:16-alpine
```

Run a container:
```
docker run --name postgres16 -p 5432:5432 -e POSTGRES_USER=root -e POSTGRES_PASSWORD=secret -d postgres:16-alpine
```

### Test Postgres Installation

And you can do this after run Postgres container:
```
docker exec -it postgres16 psql -U root
```

And you can see this on your CLI:
```
psql (16.3)
Type "help" for help.

root=#
```

Try to run this simple query:
```
select now();
```

Note: According to https://hub.docker.com/_/postgres, the PostgreSQL image sets up `trust` authentication locally so you may notice a password is not required when connecting from `localhost` (inside the same container). However, a password will be required if connecting from a different host/container.

## Notes about TablePlus

- Just copy & paste the sql that already exported from dbdiagram.io, and `Cmd + Enter`.
- When you need to delete the tables, don't forget to choose `Cascade`, then `Cmd + S`.

## golang-migrate

Initiate the db directory in our projects directory:
```
mkdir -p db/migration
```

Run this create to prepare database scheme migration:
```
migrate create -ext sql -dir db/migration -seq init_schema
```

For the first time, just copy and paste the exported sql (from dbdiagram.io) to `up` schema, and write drop table script on `down` schema.

Notes:
- When you drop the tables, always remember, drop the table with foreign key constraint first. For example, in our case, `transfers` and `account_mutations` must drop first, and the `accounts` table must drop in the last order, because there's a foreign key constraint in `transfers` and `account_mutations`.