# Raspberry Pi 3 B+ come server web locale
## Sicurezza
### Cambiare password agli utenti pi e root
```
sudo passwd pi
sudo passwd root
```
### Creare un nuovo utente *nomeutente*
```
sudo adduser nomeutente
```
### Creare un nuovo gruppo *nomegruppossh* e aggiungere *nomeutente* e/o l'utente *pi*
```
sudo groupadd nomegruppossh
sudo adduser nomeutente nomegruppossh
```
### Abilitare SSH e consentire l'accesso per il solo gruppo *nomegruppossh*
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
Disinstallare pacchetti non necessari da Menu > Preferenze > Add/Remove Software

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
sudo chown -R nomeutente:www-data /var/www/html/
sudo chmod -R 770 /var/www/html/
sudo apt install php php-mbstring mysql-server php-mysql -y
sudo apt install phpmyadmin -y
sudo service apache2 restart
sudo apt-get install vsftpd -y
```
### MySQL/MariaDB
Creare un nuovo utente che abbia tutti i privilegi per accedere e gestire il DB:
```
sudo mysql --user=root
GRANT ALL ON *.* TO 'nomeutente'@'192.168.xx.xx' IDENTIFIED BY 'password' WITH GRANT OPTION;
flush privileges;
exit
```
Assicurarsi che il nuovo utente abbia tutti i privilegi per accedere e cambiare password agli altri utenti, quindi cambiare password all'utente root:
```
sudo mysql --user=root
SET PASSWORD FOR root@localhost = PASSWORD('nuovapassword');
exit
```
Accedere da locale con l'utente root:
```
sudo mysql --user=root
[... provare qualche comando di update ...]
exit
```
Quindi restringere gli accessi del nuovo utente a quelli minimi indispensabili.

In alternativa per configurare MySQL/MariaDB dopo l'installazione (ricordarsi la nuova password!!!):
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
Per connettersi da remoto o con client (ad esempio DBEAVER)
Oltre a verificare l'*host* per l'utente nella tabella *user*, modificare il file */etc/mysql/my.cnf*, aggiungendo le seguenti righe alla fine del file (0.0.0.0 può essere sostituito dal singolo ip):
```
sudo nano /etc/mysql/my.cnf

[mysqld]
bind-address = 0.0.0.0
```
Riavviare il server mysql
```
sudo service mysql restart
```
## WORDPRESS
###  Abilitare Apache’s rewrite mod:
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
### WP-CLI
```
sudo curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar
sudo php wp-cli.phar --info
sudo chmod +x wp-cli.phar
sudo mv wp-cli.phar /usr/local/bin/wp
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
Collegarsi tramite client ftp (FileZilla...) via SFTP (SSH Ftp).

## PERMESSI cartelle e file
Cartelle e file devono essere di proprietà dell'utente del server web (es_www.data per apache).
```
sudo chown -R www-data:www-data nomecartella
```
Permessi da impostare:
775 per le cartelle, 664 per i file
```
sudo find nomecartella -type f -exec chmod 664 {} + -o -type d -exec chmod 775 {} +
```
