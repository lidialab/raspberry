# Raspberry Pi 3 B+ come server web locale

## Sicurezza: cambiare password agli utenti pi e root
Da Menu > Preferenze > Raspberry Pi Configuration > System > Change Password...

Da riga di comando 
```
sudo passwd pi
sudo passwd root
```
## creare un nuovo utente *nomeutente*
```
sudo adduser nomeutente
```
## creare un nuovo gruppo *nomegruppossh* e aggiungere *nomeutente* e/o l'utente *pi*
```
sudo groupadd nomegruppossh
sudo adduser nomeutente nomegruppossh
```
## Abilitare SSH, abilitare l'accesso per il solo gruppo *nomegruppossh*
Da Menu > Preferenze > Raspberry Pi Configuration > Interfaces > SSH: Enable

Abilitare l'accesso per il solo gruppo *nomegruppossh*
Aggiungere al file /etc/ssh/sshd_config la riga
```
AllowGroups nomegruppossh
```
Far ripartire il servizio ssh

```
sudo systemctl restart ssh
```
## Disinstallare pacchetti non necessari
Disinstallare pacchetti non necessari

## Aggiornare il sistema
```
sudo apt update
sudo apt upgrade
sudo apt update
sudo apt-get install clamav clamav-docs -y
```

## Installare LAMP
```
sudo apt install apache2 -y
sudo chown -R pi:www-data /var/www/html/
sudo chmod -R 770 /var/www/html/
sudo apt install php php-mbstring mysql-server php-mysql -y
sudo mysql --user=root
SET PASSWORD FOR root@localhost = PASSWORD('yourpassword');
exit
sudo apt install phpmyadmin -y
sudo service apache2 restart
sudo apt-get install vsftpd -y
```
In alternativa per configurare mysql/mariadb dopo l'installazione:
```
sudo mysql_secure_installation
Enter current password for root
Type in Y and press Enter to Set root password?
Type in a password at the New password: prompt, and press Enter
Type in Y to Remove anonymous users
Type in Y to Disallow root login remotely
Type in Y to Remove test database and access to it
Type in Y to Reload privilege tables now
```
Per connettersi con programma da altro pc (ad esempio DBEAVER), creare un utente ad hoc e dare i privilegi in base all'IP del pc
```
sudo mysql --user=root
GRANT ALL ON nomedb.* TO 'nomeutente'@'192.168.xx.xx' IDENTIFIED BY 'password' WITH GRANT OPTION;
flush privileges;
exit
sudo service mysql restart
```

## FTP
```
sudo apt-get install vsftpd
```
Modificare il file /etc/vsftpd.conf accertandosi delle seguenti configurazioni:
```
anonymous_enable=NO
local_enable=YES
write_enable=YES
force_dot_files=YES
```
Riavviare il server FTP
```
sudo service vsftpd restart
```

# WORDPRESS
Abilitare Apache’s rewrite mod:
```
sudo a2enmod rewrite
```
Aggiungere quanto segue dopo "<VirtualHost \*:80>" nel file */etc/apache2/sites-available/000-default.conf*:
```
<Directory "/var/www/html">
    AllowOverride All
</Directory>
```
Riavviare il server Apache:
```
sudo service apache2 restart
```
## WP-CLI
```
sudo curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar
sudo php wp-cli.phar --info
sudo chmod +x wp-cli.phar
sudo mv wp-cli.phar /usr/local/bin/wp
```
