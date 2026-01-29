# TP 01 – Installation d’un serveur Linux Debian

## 2. Post-Installation

### 2.1 Nombre de paquets installés

**Commande :**
```bash
dpkg -l | wc -l
```
**Résultat obtenu :**
331

**Explication :**  
Cette commande permet de compter le nombre total de paquets installés sur le système.  

### 2.2  Configuration du service SSH


**Recherche du paquet SSH :**
```bash
apt search openssh-server
```
**Résultat obtenu :**
openssh-server/stable 1:10.0p1-7 amd64 Serveur SSH standard permettant d’accéder à la machine à distance de manière sécurisée (connexion shell chiffrée)
openssh-server-gssapi/stable 1:10.0p1-7 all Variante du serveur SSH intégrant le support GSSAPI / Kerberos pour l’authentification avancée
**Explication :**  
openssh-server est le serveur SSH standard.
openssh-server-gssapi est une version avec support Kerberos, non nécessaire ici.
#### Installation du serveur SSH 
```bash
apt install openssh-server
```
#### Vérification du service
```bash
systemctl status ssh
**Résultat obtenu :**
Active: active (running)
**Explication :**  
Le serveur SSH est bien installé et actif, prêt à accepter les connexions. 
### Édition du fichier de configuration SSH
```bash
nano /etc/ssh/sshd_config
```
**Modifications :**  
```bash
PermitRootLogin yes
PasswordAuthentication yes
```
**Explication :**  
PermitRootLogin yes autorise la connexion root à distance

PasswordAuthentication yes active l’authentification par mot de passe
#### Redémarrage du service SSH
```bash
systemctl restart ssh
```
**Vérification :** 
systemctl status ssh
**Résultat obtenu :**
Active: active (running)
**Explication :**  
Le service SSH a été redémarré et la nouvelle configuration est appliquée. 
### 2.3 Connexion depuis la machine hôte
### Récupération de l’adresse IP de la VM
```bash
ip a
```
**Résultat obtenu :**
10.0.2.15
**Explication :**  
Permet de connaître l’adresse IP de la machine virtuelle pour se connecter en SSH.
#### Connexion SSH depuis la machine hôte
```bash
ssh root@10.0.2.15
```
**Explication :** 
La connexion SSH fonctionne et permet de travailler depuis le terminal de la machine hôte, sans utiliser la console VirtualBox.
### 2.4 Utilisation de l’espace disque
**Commande :** 
```bash
df -h
```
**Explication :**
/ est très léger (<1 Go)

/tmp et /var/log sont des partitions séparées

swap est utilisé pour l’échange mémoire
### 2.5 Commandes à expliquer
**Locales :** 
```bash
echo $LANG
```
**Résultat obtenu :**
fr_FR.UTF-8
**Explication :**
Indique la locale principale (langue + encodage). Ici, français UTF-8.

**Nom de la machine :** 
```bash
hostname
```
**Résultat obtenu :**
serveur1
**Explication :**
Affiche le nom de la machine défini lors de l’installation.

**Nom de domaine :** 
```bash
hostname -f
```
**Résultat obtenu :**
serveur1.ufr-info-p6.jussieu.fr
Affiche le nom complet de la machine .
#### Vérification des dépôts APT
```bash
cat /etc/apt/sources.list | grep -v -E '^#|^$'
```
**Résultat obtenu :**
deb http://deb.debian.org/debian bookworm main
deb http://security.debian.org/debian-security bookworm-security main
**Explication :**
Affiche uniquement les dépôts actifs, confirmant l’utilisation des dépôts officiels Debian.
### Comptes utilisateurs et mots de passe
```bash
cat /etc/passwd | grep -vE 'nologin|sync'
cat /etc/shadow | grep -vE ':\*:|:!\*:'
```
**Explication :**
/etc/passwd → comptes utilisateurs réels

/etc/shadow → comptes avec mot de passe actif
Seul le compte root est actif sur cette installation .
