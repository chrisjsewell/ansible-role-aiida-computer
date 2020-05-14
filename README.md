[![Build Status](https://travis-ci.org/chrisjsewell/ansible-role-aiida-computer.svg?branch=master)](https://travis-ci.org/chrisjsewell/ansible-role-aiida-computer)

# Ansible Role: chrisjsewell.aiida-computer

An ansible role that creates an AiiDA computer.
For SSH transport type computers, this role also sets up a secure connection configuration by:

- Copying the main/proxy private keys to the host.
- Adding configuration blocks to `~/.ssh/config`.
- Adding the main/proxy servers public key to `known_hosts`.

Assuming, the accompanying public key has been added to the `~/.ssh/authorized_keys` on the computer/proxy servers, this should ensure that no user input is required to initiate a connection.

NOTE: Currently it is assumed that the private key(s) have no set passphrase.
Make sure these keys are not left on an insecure host!

It is hoped that this restriction will eventually be removed, which would involve the set up of a persistent `ssh-agent`, with keys loaded, such as using the [keychain](https://linux.die.net/man/1/keychain) package.

## Installation

`ansible-galaxy install chrisjsewell.aiida-computer`

## Role Variables

See `defaults/main.yml`.
`aiida_computer` will be merged with `aiida_computer_defaults`, to create the full input variable set.

## Example Playbook

Minimal example to set-up a default localhost computer.

```yaml
- hosts: all
  roles:
  - role: chrisjsewell.aiida-computer
    vars:
      aiida_computer:
        setup_label: my_computer

```

To specify a specific verdi executable and profile name, set the variable:
`aiida_verdi_exec: /usr/bin/verdi -p profile_name`

To set-up an ssh computer (no proxy).

```yaml
- hosts: all
  roles:
  - role: chrisjsewell.aiida-computer
    vars:
      aiida_computer:
        setup_label: ssh_computer
        setup_hostname: server.example.com
        setup_transport: ssh
        config_username: username
        config_auth_key: ~/.ssh/epfl.pem
        config_host_key: server.example.com ssh-rsa ASDeararAIUHI324324...

```

- `config_auth_key` is a path on your local machine and can be be created using `ssh-keygen -t rsa -b 4096 -m PEM`. Its public version should be copied in the `~/.ssh/authorized_keys` file on the server.
- `config_host_key` can usually be obtained by looking on the server at `/etc/ssh/ssh_host_rsa_key.pub`, or using `ssh-keyscan -t rsa server.example.com`.

To include proxy servers, you should also specify `config_proxy_netcat`. This should be a path to `netcat` on the proxy server (or `-W` works on newer OS, [see here](https://stackoverflow.com/a/29176698/5033292)).


## Development and testing

This role uses [Molecule](https://molecule.readthedocs.io/en/latest/#) and [Docker](https://www.docker.com/) for tests.

After installing [Docker](https://www.docker.com/):
```bash
git clone https://github.com/chrisjsewell/ansible-role-aiida-computer chrisjsewell.aiida-computer
# Note: folder name chrisjsewell.aiida-computer is required for running tests
cd chrisjsewell.aiida-computer
pip install -r requirements.txt  # Installs molecule
molecule test  # runs tests
```

## License

MIT
