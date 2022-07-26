# Setup and Configure Ansible Collection Bot

## Requirements
---------------
- Podman

### Steps to install and configure Collection Bot
-------------------------------------------------
- Pull the Fedora 36 image
```bash
podman pull fedora:36
```

- Run Podman Fedora:36 container
```bash
podman run -dt --name=collectionbot-community.network fedora:36 bash
```

- Exec into the newly created container
```bash
podman exec -it collectionbot-community.network bash
```

- Install `git` and clone `collection_bot` repo:
```bash
dnf install git -y

mkdir /var/lib/collectionbot

git clone https://github.com/ansible-community/collection_bot.git -b upstream /var/lib/collectionbot/collection_bot
```

- Create Python virtualenv `venv` and install required dependencies needed for collection_bot
```bash
python3 -m venv /var/lib/collectionbot/venv

/var/lib/collectionbot/venv/bin/pip install -r /var/lib/collectionbot/collection_bot/requirements.txt
```

- Configure `/etc/ansibullbot/ansibullbot.cfg`
```bash
mkdir /etc/ansibullbot

vi /etc/ansibullbot/ansibullbot.cfg

#copy the below and replace the values accordingly
[defaults]
github_repos=ansible-collections/community.network
github_username=ansibullbot
github_token=ghp_ANSIBULLBOT_PAT_TOKEN
github_maintainers=ansible-collections/community-team,ansible-collections/provenmaintainers

[azp]
org=ansible
project=AZP_PROJECT_ID_OF_COLLECTION
```

- Change to `/var/lib/collectionbot` directory and execute `./triage_ansible.py` to run collection_bot
```bash

/var/lib/collectionbot/venv/bin/python /var/lib/collectionbot/collection_bot/triage_ansible.py --daemonize --daemonize_interval=300 --force --logfile /var/log/ansibullbot.log
```