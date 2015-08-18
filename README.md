# aws-cli through Docker

[Universal Command Line Interface](https://github.com/aws/aws-cli) for **Amazon Web Services** through **Docker**.

## Usage

```bash
docker run --rm -i \
-e AWS_ACCESS_KEY_ID=[your-aws-access-key-id] \
-e AWS_SECRET_ACCESS_KEY=[your-ws-secret-access-key] \
creativearea/aws [options] <command> <subcommand> [parameters]
```

You can also use a config file to tell AWS cli about your credentials:

```bash
docker run --rm -i \
-v $(pwd)/config:/root/.aws
creativearea/aws [options] <command> <subcommand> [parameters]
```

> Please refer to [Getting Started](https://github.com/aws/aws-cli#getting-started) and the [Configurable Variables](https://github.com/aws/aws-cli#other-configurable-variables) sections of the AWS Cli documentation.

## Use case

Let's say for example you want to periodically backup your MySQL database (running in another Docker container) to an S3 bucket. You could setup a daily cronjob on your host that run the following command:

```bash
docker run --rm -i \
--link mysql:mysql \
mysql \
sh -c 'exec mysqldump -h"$MYSQL_PORT_3306_TCP_ADDR" -P"$MYSQL_PORT_3306_TCP_PORT" -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD" my-app-database' \
| \
docker run --rm -i \
-e AWS_ACCESS_KEY_ID=xxxxxxxxxxxx \
-e AWS_SECRET_ACCESS_KEY=xxxxxxxxxx \
creativearea/aws s3 cp - s3://tweenpics-mysql-backups/mysql_backup_$(date "+%y%m%d").sql
```
