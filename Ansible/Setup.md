```
sudo apt update
```
```
sudo apt upgrade
```
```
sudo useradd -m ansible
```
```
sudo passwd ansible
```
```
sudo nano /etc/ssh/sshd_config
  PasswordAuthentication  yes
```
```
sudo systemctl restart sshd
```
```
sudo visudo
  ansible  ALL=(ALL)  NOPASSWD: ALL
```
```
sudo apt install ansible
```
```
ansible --version
```
<details><summary><b>for centos</b></summary>

sudo yum install epel-release
sudo yum update
sudo yum install ansible

* https://centos.pkgs.org/7/centos-extras-x86_64/sshpass-1.06-2.el7.x86_64.rpm.html
```
wget http://mirror.centos.org/centos/7/extras/x86_64/Packages/sshpass-1.06-2.el7.x86_64.rpm
```
```
sudo yum localinstall sshpass-1.06-2.el7.x86_64.rpm
```
-----
</details>
  
```
sudo nano /etc/hosts
  ip  node1
  ip  nod2
```
```
mkdir ansible
cd ansible
```
```
nano ansible.cfg
  [defaults]
  inventory = hosts
  remote_user = ansible
```
```
nano hosts
  [nodes]
  node1 ansible_host=<PrivateIP of ansible_node1 instance>
  node2 ansible_host=<PrivateIP of ansible_node2 instance>
```
```
ssh-keygen
cd /home/ansible
ssh-copy-id ansible@node1
```
```
nano /tmp/hosts
  node1
  node2
cat /tmp/hosts | while read line; do ssh-copy-id ansible@$line; done
```
```
ansible all --list
ansible nodes -m ping
ansible all -m ping
```
