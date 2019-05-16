How to setup a Smarkaklink dev/test env
---------------------------------------

Requirements
============

Ruby >= 2.6
bundler
Postgresql >= 9

Repositories
============

Clone the following repositories:

```bash
git clone --branch shg_master https://github.com/CIRALabs/shg_mud_supervisor.git
git clone --branch shg_master https://github.com/CIRALabs/shg_reach.git
git clone --branch shg_master https://github.com/CIRALabs/shg_highway.git
```

Install dependencies and configuration
======================================

`cd` into each of the repositories and run:

```bash
git submodule init --update
bundle install --path vendor/bundle
```

Then, prepare and configure the DB:

Only for `shg_highway` and `shg_mud_supervisor`: run the `make` command and execute the last output of the command (a `cp` command in a line starting with "You can enable by:").

For all repositories: initialize the database:

```bash
RAILS_ENV=development bundle exec rake db:migrate
RAILS_ENV=development bundle exec rake db:fixtures:load
```

Edit your `/etc/hosts` file to add the following entry:
```
::1 n3CE618.router.securehomegateway.ca
```

Start servers
=============
Start the MASA server:

```bash
cd shg_highway
RAILS_ENV=development ./bin/server
```

Then start the AR server:

```bash
cd shg_mud_supervisor
RAILS_ENV=development ./bin/startshg
```

Client
======

You can exercise the kaklink protocol using the CLI client `shg_reach`:

```bash
cd shg_reach
./sk/step03.sh
```
