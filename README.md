# Vagrant DEV Box

## Getting started

### SSH Keys
- Place `id_rsa` and `id_rsa.pub` files into the `provision/.ssh` folder.
- Keys will be copied, and chowned + chmod-ed for the Box's user to prevent all possible issues which could occur if host is different platform.

## Run
```
vagrant up
```

## Changing provision contents
```
vagrant provision
```

## Contents
- GUI: Ubuntu Desktop
- VSCode: telemetry turned off
- Docker
- Terraform
- Ansible
- NVM: With Node `v14.15.4` installed by `default`
- IDEA Ultimate
- OpenJDK 11: `v11.0.10+9`
- Maven: `v3.6.3`
