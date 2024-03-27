# Ansible Ad-hoc Commands
Some Usefull Commands:
```
ansible all -a "hostname"
```
add the argument `-f 1` to tell Ansible to use only one fork:
```
ansible all -a "hostname" -f 1
```
```
ansible all -a "df -h"
```
```
ansible all -a "free -m"
```
```
ansible all -a "date"
```

The `-b` option (alias for --become) run commands with `sudo`.

you can pass in -K (alias for `--ask-become-pass`), so you can enter your password when **Ansible** needs it.
```
ansible web -b -m apt -a "name=httpd state=present"
```

```
ansible db -b -m apt -a "name=mariadb-server state=present"
```
```
ansible db -b -m service -a "name=mariadb state=started enabled=yes"
```
```
ansible db -b -a "iptables -F"
```
```
ansible db -b -a "iptables -A INPUT -s 192.168.60.0/24 -p tcp \
-m tcp --dport 3306 -j ACCEPT"
```
```
ansible db -b -m yum -a "name=MySQL-python state=present"
```
```
ansible db -b -m mysql_user -a "name=django host=% password=12345 priv=*.*:ALL state=present"
```
```
ansible web -b -a "service apache2 restart" --limit "192.168.10.20"
```

<b>Limit hosts with a simple pattern (asterisk is a wildcard):</b>
```
ansible web -b -a "service apache2 restart" --limit "*.20"
```

### Manage users and groups
```
ansible web -b -m group -a "name=admin state=present"
```
```
ansible web -b -m user -a "name=johndoe group=admin createhome=yes"
```
```
ansible web -b -m user -a "name=johndoe state=absent remove=yes"
```

### Manage packages
If you want to install a generic package like git on any Debian, RHEL, Fedora, Ubuntu, CentOS, FreeBSD, etc. system, you can use the command:
```
ansible app -b -m package -a "name=git state=present"
```

### Manage files and directories

* Get information about a file:
```
ansible multi -m stat -a "path=/etc/environment"
```

* Copy a file to the servers:
```
ansible multi -m copy -a "src=/etc/hosts dest=/tmp/hosts"
```

* Retrieve a file from the servers:
```
ansible multi -b -m fetch -a "src=/etc/hosts dest=/tmp"
```

* Create directories and files:
ansible multi -m file -a "dest=/tmp/test mode=644 state=directory"
```
ansible multi -m file -a "dest=/tmp/test state=absent"
```

