Introduction
============

Ansible playbooks are provided to:

1. Install PostgreSQL Build Farm client
2. Configure an animal
3. Run a test against all branches
4. Configure cron

Setup
=====

Use `inventory.yaml.sample` as template to create a Ansible inventory file for
each of your animals:

1. Create an `ANIMAL` section for each animal you have.
2. Replace `ANIMAL` with the name of the animal.
3. Replace `CHANGME` with the secret for that animal.
4. Set `ansible_host` to the address of the system.
5. Set `build_farm_version` to the version of the build farm software to
   install.  Typically you want the latest available.
6. Set `install_path` to where to install the build farm software.
7. Set `email` to the address for failure notices.
8. Set `hour` and `minute` globally or per animal for the cron configuration
   playbook.
9. Change or remove any other settings.

After the inventory file is created, run::

    ansible-playbook -i inventory.yaml playbook-setup.yaml

You may want to also do a test run::

    ansible-playbook -i inventory.yaml playbook-test.yaml

Update the cron table to run automatically::

    ansible-playbook -i inventory.yaml playbook-enable.yaml

Extra Ansible Notes
===================

These are tips or reminders for those who don't use Ansible enough.

1. Something went wrong, and you can't see what, maybe `-vvv` will show you::

    ansible-playbook -vvv -i inventory.yaml playbook-setup.yaml

2. Run any playbook for a specific subset of animals using `--limit`::

    ansible-playbook -i inventory.yaml --limit 'animal1,animal2` playbook-enable.yaml
