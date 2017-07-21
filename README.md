## General commands

Function test

    ansible all -m ping         // no ICMP, but Python-check -> fails on Cisco IOS

Show module documentation

    ansible-doc ios_config

Send RAW-command to host group 'wan-router'

    ansible wan-router -m raw -a "traceroute 172.16.95.1"

    
## Playbook commands

Run playbook with a specific inventory file "nxos"

    ansible-playbook -i inventory/nxos nxos-facts.yml

Run playbook that includes e.g. `hosts: all` only on a specific host or group

    ansible-playbook -i inventory/nxos --limit "access-dc" nxos-facts.yml
    
Run playbook with credentials stored in vault file, asking for password to decrypt the vault file

    ansible-playbook -i inventory/ios --limit "access-nu" --ask-vault-pass ios-facts.yml
    
Run config playbook with variable passed via cli command, e.g. a file containing config lines

    ansible-playbook -i inventory/ios --ask-vault-pass ios-config.yml --extra-vars "src=./configs/ios-general.cfg"
    ansible-playbook -i inventory/ios --ask-vault-pass ios-command.yml --extra-vars "cmd='show run'"


## Ad-hoc commands (`-a`) and some simple modules

Run command on hosts as another user

    ansible webservers -a "/bin/echo hello" -u username
    
Run command as sudo on remote machine (privilege escalation)

    ansible webservers -a "/bin/echo hello" -u username -K

Transfer file (scp) to hosts with 'copy' module

    ansible webservers -m copy -a "src=~/ansible-test/test.txt dest=/tmp"
    ansible webservers -a "ls -l /tmp"
    
Create directory with 'file' module

    ansible webservers -m file -a "dest=/path/to/c mode=755 owner=mdehaan group=mdehaan state=directory"
    
Check if a package is installed with 'yum' or 'apt' module

    ansible webservers -m yum -a "name=vi state=present"
    
Create user with 'user' module

    ansible all -m user -a "name=foo password=<crypted password here>"

Check if a service is running with 'services' module / restart service

    ansible webservers -m service -a "name=ntpd state=started"
    ansible webservers -m service -a "name=ntpd state=restarted"

Gather all the facts of hosts

    ansible webservers -m setup


## Vault handling

Encrypt unencrypted files

    ansible-vault encrypt foo.yml
    
Edit encrypted files

    ansible-vault edit foo.yml
    
Change encryption password of file

    ansible-vault rekey foo.yml


## Pubkey authentication on Cisco devices

Ansible host

    ssh-keygen
    fold -b -w 72 ~/.ssh/id_rsa.pub

Cisco device
```
conf t
ip ssh pubkey-chain
username ben
key-string
ssh-rsa MYPUBKEYblablabla1234567890QkpeSId0rhFDaY000bnG5lb0NrHEfLVmPY/Lq
rVoACDx6xXQ5gXKKFHVj4FiKcUODH#GClSlcheypp/AxclS3LmKjJXqrNWn+zUTjS29KmkCG
Q1k+l4mF1UBVk3aJggKm1vehn2d6FOpfo/8eSK4pca8yBBtNfgLhpCZlt1vP2aHr1rgsPAuj
dktGleRgb/xL2npmETF3Cy9OyyHNNaYoM9jzcZ6vTR0DYO6GcSUojMygVZbgRWsPaj7npp#A
w6RNzJx7zbDRc5A+gG/aheI2+tKekcWlYmy5X1dVMGRCnItir4nBENwlfuV1fVt2diUr8B8I
vxTu8QJmnh9m+2hG/xRJ ben@ubn
exit
exit
exit
username ben privilege 15
```


## Ansible config

    sudo vi /etc/ansible/ansible.cfg
    
    host_key_checking = False


## Ansible host file examples

```
[group xyz]
172.16.31.110
gp-wan-2-data

# define ranges
www[01:20].example.com

# define an alias and a port
jumper ansible_port=5555 ansible_host=192.0.2.50
```


