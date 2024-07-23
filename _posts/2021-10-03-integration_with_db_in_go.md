---
title: "Integration with postgres db in Go"
tags: ["go", "postgres", "database"]
toc: true
---

# Introduction

We can integrate with postgres db with [sqlx](https://github.com/jmoiron/sqlx) library.
We'll use [golang-migrate](https://github.com/golang-migrate/migrate) to setup migrations.

# Integrating with go service

## Connecting with Postgres

Read the required configuration fro env to form connection url. It's better to have separate env variables for configuration
for easy maintenance/debugging instead of single `DB_URL`
```
db_host="localhost"
db_user="gopher"
db_password="go"
db_name="simple_api"
db_ssl_mode="disable"
db_port="5432"
```
with this mapped to a struct `config` we can create the url with the function definition

```go
type Config struct {
	Driver            string `default:"postgres"`
	Host              string `required:"true"`
	User              string `required:"true"`
	Password          string `required:"true"`
	Port              int    `default:"5432"`
	MaxIdleConns      int    `split_words:"true" default:"20"`
	MaxOpenConns      int    `split_words:"true" default:"30"`
	MaxConnLifetimeMs int    `split_words:"true" default:"1000"`
	Name              string `split_words:"true" required:"true"`
	SslMode           string `split_words:"true" default:"disable"`
	AesKey            string `split_words:"true" required:"true"` // for encryption purpose
}

func (db config) url() string {
	return fmt.sprintf("user=%s password=%s host=%s port=%d dbname=%s sslmode=%s", db.user, db.password, db.host, db.port, db.name, db.sslmode)
}
```

we can pass this url `user=gopher password=go host=localhost port=5432 dbname=simple_api sslmode=disable` to sqlx and get a `db` connection
```go
db, err := sqlx.open(cfg.driver, cfg.url())
```

> Ensure you don't leak db connections or create it for every HTTP request, prefer to have connection pool. Monitor the metrics and set thresholds based on usage properly.

```go
func NewDB(cfg Config) (*sqlx.DB, error) {
	var err error
	db, err := sqlx.Open(cfg.Driver, cfg.URL())
	if err != nil {
		return nil, fmt.Errorf("error opening conn to db: %v", err)
	}
	if err := db.Ping(); err != nil {
		return nil, err
	}
	db.SetMaxIdleConns(cfg.MaxIdleConns)
	db.SetMaxOpenConns(cfg.MaxOpenConns)
	db.SetConnMaxLifetime(cfg.MaxConnLifetime())
	return db, nil
}
```

## Migrations
* Before running migrations, ensure you've postgres up and running by connecting with `psql -u gopher -d simple_api`. If it's remote db you can connect & verify with following command
```shell
source .env
psql "postgres://${DB_USER}:${DB_PASSWORD}@${DB_HOST}:${DB_PORT}/${DB_NAME}?sslmode=${DB_SSL_MODE}"
```

* create your migration files in `migrations` folder, ideally i like to keep the migrations in the service so it's clear which service owns the data.

create a file with `01_create_users.up.sql` you can use unix_epoch as well, since the time's tracked in git i use sequential index to easily navigate migrations
```sql
CREATE TABLE users (
    id varchar NOT NULL,
    name varchar NOT NULL,
    bio varchar,
    created_at timestamp with time zone DEFAULT (now() at time zone 'utc')
);

```
You can run the migrations with
```bash
migrate -verbose -path ${MIGRATION_DIR} -database "postgres://${DB_USER}:${DB_PASSWORD}@${DB_HOST}:${DB_PORT}/${DB_NAME}?sslmode=${DB_SSL_MODE}" up
```
which'll create a table. 

For every migration, we need to have undo/down migration. In this case `01_create_users.down.sql` witch `DROP TABLE users` so we can rollback with `migrate -database ${DB_URL} down 1`

checkout this [Makefile](https://github.com/devdinu/simple-go-api/blob/master/Makefile) for complete actions we can do on db with migrate.

## Querying DB
At last, you need to query the db tables to fetch data. we'll depend on go module [sqlx](https://github.com/jmoiron/sqlx). 

With the db schema we've for users table, we need to create a struct to read values
```go
type User struct {
	ID        string         db:"id"`
	Name      string         db:"name"`
	Bio       sql.NullString db:"bio"`
	CreatedAt time.Time      db:"created_at"`
}
```

`db:"db_field_name"` takes care of the mapping fields to struct, **ensure you've don't define the struct fields as private**. once you've this you can 
create an instance to a struct say `userStore` which holds `db *sqlx.DB`

Use the following code which'll fetch the list of users from db.

```go
func (s userStore) FetchUsers(ctx context.Context) ([]User, error) {
	query := s.db.Rebind(`select * from users`)
	var users []User
	err := s.db.Select(&users, query)
	if err != nil {
	    return nil, fmt.Errorf("error fetching users: %w", err)
	}
	return users, nil
}
```

> You can checkout [simple-go-api](https://github.com/devdinu/simple-go-api) repository for complete reference and setup. Checkout the [screencast recording](https://youtu.be/jzfTSNrHPjk) for more information along with demo.

# Debugging
* If postgres config `pg_hba.conf` doesn't have user specific auth enabled (peer/md5 authentication) then it won't prompt for password when you connect from [cli](cli)
* You can enable logs in config file, run `show config_file;` query and add `log_statement=all`, so it's easier to debug queries when it fails and verify whether it's running properly
* If you've written bad query and executed it, it'll be marked as dirty, so you need to verify DB is fine, fix query force the version and rerun it. Checkout the [doc](https://github.com/golang-migrate/migrate/blob/master/GETTING_STARTED.md#forcing-your-database-version) for more information

# References
* [Guide to sqlx](https://jmoiron.github.io/sqlx/)
