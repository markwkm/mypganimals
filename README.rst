Introduction
============

Shell scripts using Salt SSH are provided to help:

1. Install OS dependencies for building PostgreSQL
2. Install PostgreSQL Build Farm client
3. Create build-farm.conf
4. Run tests
5. Configure cron

Setup
=====

Use ``salt/roster.example`` as template to create a Salt roster file that
contains the details for each animal.

For each animal:

1. Set ``host`` to match the corresponding host.
2. Set ``secret`` to the animal's secret from buildfarm.postgresql.org.
3. Set ``install_path`` to where to install the build farm client.
4. Set ``email`` to the address for failure notices.
5. Set ``hour`` per animal for cron configuration.
6. Change or remove any other settings as needed.

After the inventory file is created, install OS dependencies::

    ANIMALS="slonik,elephant" bin/setup-os

Then install and configure the Build Farm client::

    ANIMALS="slonik,elephant" bin/download-client
    ANIMALS="slonik,elephant" bin/setup-client

You may want to also do a test run::

    ANIMALS="slonik,elephant" bin/test-single-build
    ANIMALS="slonik,elephant" bin/test-all-branches

Update the cron table to run automatically::

    ANIMALS="slonik,elephant" bin/enable-cron
