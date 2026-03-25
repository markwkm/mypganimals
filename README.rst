PostgreSQL Build Farm - Skonfig Configuration
=============================================

Configuration management for PostgreSQL Build Farm animals using
`skonfig <https://skonfig.li/>`_, a shell-based configuration management
tool that doesn't require Python on managed hosts.

Features:

1. Install OS dependencies for building PostgreSQL
2. Install PostgreSQL Build Farm client
3. Configure animals
4. Run test builds
5. Configure cron jobs

Prerequisites
=============

Install skonfig on your control machine::

    pip install skonfig

    # or from source:
    git clone https://code.ungleich.ch/skonfig/skonfig.git
    cd skonfig && pip install .

No software is required on managed hosts beyond SSH and a POSIX shell.

Directory Structure
===================

::

    conf/
    ├── config                       # Skonfig settings
    ├── manifest/
    │   ├── init                     # Default manifest (os + setup + enable)
    │   ├── common                   # Shared setup (sourced by other manifests)
    │   ├── os                       # Install OS packages only
    │   ├── setup                    # Set up animals only
    │   ├── enable                   # Enable cron jobs only
    │   ├── test                     # Run test builds only
    │   ├── global.conf              # Global variables
    │   └── hosts/                   # Per-host directories
    │       └── <hostname>/          # One directory per host
    │           └── <animal>.conf    # One file per animal
    └── type/
        ├── __buildfarm_packages/    # OS package installation
        ├── __buildfarm_setup/       # Build Farm client setup
        ├── __buildfarm_cron/        # Cron job configuration
        └── __buildfarm_test/        # Test build execution
    inventory                        # Host list (one per line)
    ssh_config                       # SSH connection settings

Configuration
=============

Skonfig Settings
----------------

Settings are in ``conf/config``::

    [skonfig]
    remote_out_path = /tmp/skonfig
    conf_dir = conf
    remote_exec = ssh -F ssh_config

**Important:** Set ``SKONFIG_PATH`` so skonfig can find the config::

    export SKONFIG_PATH=$PWD/conf

Global Settings
---------------

Edit ``conf/manifest/global.conf``::

    BUILD_FARM_VERSION="20"
    EMAIL="buildfarm@enterprisedb.com"
    MINUTE=30

Adding a Host
-------------

1. Add the hostname to ``inventory``
2. Add an entry to ``ssh_config``::

    Host rhel8-x86
        HostName 35.161.3.47
        User ec2-user
        IdentityFile ~/Terraform/buildfarm/rhel8-x86/ssh-id_rsa

Adding an Animal
----------------

Create a file at ``conf/manifest/hosts/<hostname>/<animal>.conf``::

    secret="your_secret_here"
    cc="gcc"
    hour="8"
    install_path="/home/ec2-user"
    config_opts_extra="--with-tclconfig=/usr/lib64"

Usage
=====

Set up environment (required)::

    export SKONFIG_PATH=$PWD/conf

Available Manifests
-------------------

Use ``-i`` to select which manifest (playbook) to run:

==================== ==========================================
Manifest             Description
==================== ==========================================
``conf/manifest/init``   os + setup + enable (default)
``conf/manifest/os``     Install OS packages only
``conf/manifest/setup``  Set up animals only
``conf/manifest/enable`` Enable cron jobs only
``conf/manifest/test``   Run test builds only
==================== ==========================================

Examples
--------

Configure a host (os + setup + enable)::

    skonfig rhel8-x86

Install packages only::

    skonfig -i conf/manifest/os rhel8-x86

Set up animals only::

    skonfig -i conf/manifest/setup rhel8-x86

Enable cron jobs only::

    skonfig -i conf/manifest/enable rhel8-x86

Run a test build::

    skonfig -i conf/manifest/test rhel8-x86

Configure all hosts::

    for host in $(cat inventory); do skonfig "$host"; done

Configure all hosts (stop on first failure)::

    for host in $(cat inventory); do skonfig "$host" || exit 1; done

Troubleshooting
===============

Verbose/debug output::

    skonfig -v rhel8-x86   # verbose
    skonfig -vv rhel8-x86  # debug
    skonfig -n rhel8-x86   # dry run

SSH connection issues - test connectivity::

    ssh -F ssh_config rhel8-x86

Why Skonfig?
============

- **No Python required on managed hosts** - Only needs SSH and POSIX shell
- **Works with any Python version** - No compatibility issues between old/new systems
- **Simple shell-based configuration** - Easy to understand and debug
- **Idempotent by design** - Safe to run multiple times
