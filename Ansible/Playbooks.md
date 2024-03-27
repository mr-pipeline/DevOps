# Ansible Playbook

ansible-playbook playbook.yml

<details><b>Ex. 01</b>
```
---
- hosts: all
  tasks:
  - name: Install Apache.
    command: yum install --quiet -y httpd httpd-devel
  - name: Copy configuration files.
    command: >
      cp httpd.conf /etc/httpd/conf/httpd.conf
  - command: >
      cp httpd-vhosts.conf /etc/httpd/conf/httpd-vhosts.conf
  - name: Start Apache and configure it to run at boot.
    command: service httpd start
  - command: chkconfig httpd on
```
</details>

<details><summary><b>Ex. 02</b></summary>
---
- hosts: all
  become: yes

  tasks:
    - name: Install Apache.
      yum:
        name:
          - httpd
          - httpd-devel
        state: present
    - name: Copy configuration files.
      copy:
        src: "{{ item.src }}"
        dest: "{{ item.dest }}"
        owner: root
        group: root
        mode: 0644
      with_items:
        - src: httpd.conf
          dest: /etc/httpd/conf/httpd.conf
        - src: httpd-vhosts.conf
          dest: /etc/httpd/conf/httpd-vhosts.conf
    - name: Make sure Apache is started now and at boot.
      service: name=httpd state=started enabled=yes
</details>

