---
title: Console applications
currentMenu: console-applications
introduction: Learn how to run serverless console commands on AWS Lambda with Symfony Console or Laravel Artisan.
---

Bref provides a way to run console commands on AWS Lambda.

This can be used to run the [Symfony Console](https://symfony.com/doc/master/components/console.html), [Silly](https://github.com/mnapoli/silly) or Laravel Artisan commands in production.

## Configuration

The lambda function used for running console applications must use two Lambda layers:

- the base PHP layer (the PHP runtime that provides the `php` binary)
- the "console" layer that overrides the base runtime to execute your console application

Below is a minimal `template.yaml`. To create it automatically run `vendor/bin/bref init`.

```yaml
Resources:
    MyFunction:
        Type: AWS::Serverless::Function
        Properties:
            FunctionName: 'my-function'
            CodeUri: .
            Handler: bin/console # or `artisan` for Laravel
            Layers:
                # PHP runtime
                - 'arn:aws:lambda:<region>:416566615250:layer:php-72:<version>'
                # Console layer
                - 'arn:aws:lambda:<region>:416566615250:layer:console:<version>'
```

## Usage

To run a console command on AWS Lambda use `bref cli`:

```bash
vendor/bin/bref cli -- <command>
```

Pass your command, arguments and options by putting them after `--`. The `--` delimiter separates between options for the `bref cli` command (before `--`) and your command (after `--`).

```bash
vendor/bin/bref cli <bref options> -- <your command, your options>
```

For example:

```bash
# Runs the CLI application without arguments and displays the help
$ vendor/bin/bref cli
# ...

$ vendor/bin/bref cli -- doctrine:migrate
Your database will be migrated.
To execute the SQL queries run the command with the `--force` option.

$ vendor/bin/bref cli -- doctrine:migrate --force
Your database has been migrated.
```