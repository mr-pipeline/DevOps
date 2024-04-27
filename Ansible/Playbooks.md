# Ansible Playbook

ansible-playbook playbook.yml

<details><summary><b>Ex. 01</b></summary>

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

```
---
- hosts: all
  become: yes  //tells Ansible to run all the commands through sudo, so the commands will be run as the root user.

  tasks:
    - name: Install Apache.
      yum:
        name:
          - httpd
          - httpd-devel
        state: present  //We tell yum to make sure the packages are installed with state: present, but we could also use state: latest to ensure the latest version is installed, or state: absent to make sure
      when: ansible_distribution == "Ubuntu"
the packages are not installed.
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
```
</details>

```
ansible-playbook playbook.yml --limit webservers
```

```
ansible-playbook playbook.yml --limit xyz.example.com
```

If you want to see a list of hosts that would be affected by your playbook before you actually run it, use `--list-hosts`:

```
ansible-playbook playbook.yml --list-hosts
```

You can explicitly define a remote user to use for remote plays using the `--user (-u)` option:

```
ansible-playbook playbook.yml --user=johndoe
```

In some situations, you will need to pass along your sudo password to the remote
server to perform commands via sudo. In these situations, you’ll need use the
`--ask-become-pass (-K)` option. You can also explicitly force all tasks in a playbook
to use sudo with `--become (-b)`. Finally, you can define the sudo user for tasks run
via sudo (the default is root) with the `--become-user (-U)` option.

```
ansible-playbook playbook.yml --become --become-user=janedoe --ask-become-pass
```

<details><summary><b>Ex. 03</b></summary>

```  
---
- hosts: all
  become: yes

  vars_files:
    - vars.yml

  pre_tasks:
    - name: Update apt cache if needed.
      apt: update_cache=yes cache_valid_time=3600

  handlers:
    - name: restart apache
      service: name=apache2 state=restarted

  tasks:
    - name: Get software for apt repository management.
      apt:
        state: present
        name:
          - python3-apt
          - python3-pycurl

    - name: Add ondrej repository for later versions of PHP.
      apt_repository: repo='ppa:ondrej/php' update_cache=yes

    - name: "Install Apache, MySQL, PHP, and other dependencies."
      apt:
        state: present
        name:
          - acl
          - git
          - curl
          - unzip
          - sendmail
          - apache2
          - php8.2-common
          - php8.2-cli
          - php8.2-dev
          - php8.2-gd
          - php8.2-curl
          - php8.2-opcache
          - php8.2-xml
          - php8.2-mbstring
          - php8.2-pdo
          - php8.2-mysql
          - php8.2-apcu
          - libpcre3-dev
          - libapache2-mod-php8.2
          - python3-mysqldb
          - mysql-server

    - name: Disable the firewall (since this is for local dev only).
      service: name=ufw state=stopped

    - name: "Start Apache, MySQL, and PHP."
      service: "name={{ item }} state=started enabled=yes"
      with_items:
        - apache2
        - mysql

    - name: Enable Apache rewrite module (required for wordpress).
      apache2_module: name=rewrite state=present
      notify: restart apache

    - name: Add Apache virtualhost for wordpress.
      template:
        src: "templates/wordpress.test.conf.j2"
        dest: "/etc/apache2/sites-available/{{ domain }}.test.conf"
        owner: root
        group: root
        mode: 0644
      notify: restart apache

    - name: Enable the wordpress site.
      command: >
        a2ensite {{ domain }}.test
        creates=/etc/apache2/sites-enabled/{{ domain }}.test.conf
      notify: restart apache

    - name: Disable the default site.
      command: >
        a2dissite 000-default
        removes=/etc/apache2/sites-enabled/000-default.conf
      notify: restart apache

    - name: Adjust OpCache memory setting.
      lineinfile:
        dest: "/etc/php/8.2/apache2/conf.d/10-opcache.ini"
        regexp: "^opcache.memory_consumption"
        line: "opcache.memory_consumption = 96"
        state: present
      notify: restart apache

    - name: Create a MySQL database for wordpress.
      mysql_db: "db={{ domain }} state=present"

    - name: Create a MySQL user for wordpress.
      mysql_user:
        name: "{{ domain }}"
        password: "1234"
        priv: "{{ domain }}.*:ALL"
        host: localhost
        state: present

    - name: Download Composer installer.
      get_url:
        url: https://getcomposer.org/installer
        dest: /tmp/composer-installer.php
        mode: 0755

    - name: Run Composer installer.
      command: >
        php composer-installer.php
        chdir=/tmp
        creates=/usr/local/bin/composer

    - name: Move Composer into globally-accessible location.
      command: >
        mv /tmp/composer.phar /usr/local/bin/composer
        creates=/usr/local/bin/composer

    - name: Ensure wordpress directory exists.
      file:
        path: "{{ wordpress_core_path }}"
        state: directory
        owner: www-data
        group: www-data
```
</details>
