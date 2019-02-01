# Raspberry Pi 3 B+ come server web locale

## Sicurezza: cambiare password all'utente pi e root
Da Menu > Preferenze > Raspberry Pi Configuration > System > Change Password...

Da riga di comando 
```
sudo passwd pi
sudo passwd root
```
## creare un nuovo utente
```
sudo adduser nomeutente
```
## creare un nuovo gruppo nomegruppossh e aggiungere il nuovo utente e/o pi
```
sudo groupadd nomegruppossh
sudo adduser nomeutente nomegruppossh
```
## Abilitare SSH, abilitare l'accesso per il solo gruppo nomegruppossh
Da Menu > Preferenze > Raspberry Pi Configuration > Interfaces > SSH: Enable

Abilitare l'accesso per il solo gruppo nomegruppossh
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

