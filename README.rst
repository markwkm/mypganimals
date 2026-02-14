Introduction
============

Ansible playbooks are provided to:

1. Install OS dependencies for building PostgreSQL
2. Install PostgreSQL Build Farm client
3. Configure an animal
4. Run a test against all branches
5. Configure cron

Setup
=====

Use ``inventory.yaml.sample`` as template to create an Ansible inventory file
for each of your animals.

The inventory has two groups:

- ``hosts``: Unique physical/virtual hosts (for OS package installation)
- ``buildfarm``: Build Farm animals (for client setup and configuration)

This separation prevents package manager conflicts when multiple animals share
the same host.

For each unique host:

1. Add an entry under the ``hosts`` group with a unique name.
2. Set ``ansible_host`` to the address of the system.

For each animal:

1. Add an entry under the ``buildfarm`` group with the animal name.
2. Set ``ansible_host`` to match the corresponding host.
3. Set ``secret`` to the animal's secret from buildfarm.postgresql.org.
4. Set ``install_path`` to where to install the build farm software.
5. Set ``build_farm_version`` to the version of the build farm software
   (typically the latest available).
6. Set ``email`` to the address for failure notices.
7. Set ``hour`` and ``minute`` globally or per animal for cron configuration.
8. Change or remove any other settings as needed.

After the inventory file is created, install OS dependencies::

    ansible-playbook -i inventory.yaml playbook-os.yaml

Then install and configure the Build Farm client::

    ansible-playbook -i inventory.yaml playbook-setup.yaml

You may want to also do a test run::

    ansible-playbook -i inventory.yaml playbook-test.yaml

Update the cron table to run automatically::

    ansible-playbook -i inventory.yaml playbook-enable.yaml

Extra Ansible Notes
===================

These are tips or reminders for those who don't use Ansible enough.

1. Something went wrong, and you can't see what, maybe ``-vvv`` will show you::

    ansible-playbook -vvv -i inventory.yaml playbook-setup.yaml

2. Run any playbook for a specific subset using ``--limit``::

    ansible-playbook -i inventory.yaml --limit 'animal1,animal2' playbook-enable.yaml
