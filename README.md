# Exercise 12: Remote Control Using Ansible
## nd9991-04-CD-pipelines

```
ssh-add ~/.ssh/project02-udacity.pem
ssh ubuntu@ec2-54-162-33-162.compute-1.amazonaws.com
ansible-playbook main-remote.yml -i inventory --syntax-check
ansible-playbook main-remote.yml -i inventory --check
ansible-playbook main-remote.yml -i inventory --diff --verbose
```
