Edit `/etc/phpmyadmin/config.inc.php`

$cfg['Servers'][$i]['verbose'] = 'localhost';

$cfg['Servers'][$i]['host'] = 'localhost';

$cfg['Servers'][$i]['port'] = '3306';

$cfg['Servers'][$i]['socket'] = '';

$cfg['Servers'][$i]['connect_type'] = 'tcp';

$cfg['Servers'][$i]['extension'] = 'mysqli';

$cfg['Servers'][$i]['auth_type'] = 'HTTP';

$cfg['Servers'][$i]['user'] = '**your-root-username**';

$cfg['Servers'][$i]['password'] = '**root-password**';

$cfg['Servers'][$i]['AllowNoPassword'] = true;
