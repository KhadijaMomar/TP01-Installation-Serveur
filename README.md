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

**Explication :**
Affiche uniquement les dépôts actifs, confirmant l’utilisation des dépôts officiels Debian.
#### Comptes utilisateurs et mots de passe
```bash
cat /etc/passwd | grep -vE 'nologin|sync'
cat /etc/shadow | grep -vE ':\*:|:!\*:'
```
**Explication :**
/etc/passwd → comptes utilisateurs réels

/etc/shadow → comptes avec mot de passe actif
Seul le compte root est actif sur cette installation .
#### Vérification des partitions
```bash
fdisk -l
fdisk -x
```
**Explication :**
Vérifie la structure, taille et points de montage des partitions.
#### Vérification de l’espace disque
```bash
df -h
```
**Explication :**
La commande df -h affiche l’espace disque utilisé et disponible sur les différents systèmes de fichiers, avec des tailles lisibles (option -h)
### 3.1 installation automatique
#### preseed : `a quoi cela sert ?
Un fichier *preseed* permet d’automatiser l’installation de Debian en fournissant à l’avance toutes les réponses de l’installateur (langue, partitionnement, utilisateurs, paquets, etc.), sans intervention de l’utilisateur.
### 3.2 rescue mode
**Explication :**
Debian fournit un mode de secours (Rescue mode) permettant d’intervenir sur un système installé, même sans connaître le mot de passe root.
**Procédure :** 
```bash
Démarrer la machine avec l’ISO Debian

Choisir Advanced options

Sélectionner Rescue mode

Choisir la partition racine (/)

Sélectionner Drop to shell
```
#### Commande pour changer le mot de passe root
```bash
passwd root
```
#### Redémarrage du système
```bash
reboot
```
Cette méthode permet de récupérer l’accès administrateur sans réinstallation du système
### 3.3 redimentionnement partition
#### Question : Comment redimensionner la partition racine sans réinstaller le système ?
**Méthodes possibles et Exemple :**
```bash
parted /dev/sda resizepart 1 20G
resize2fs /dev/sda1
df -h
```
parted modifie la taille de la partition

resize2fs ajuste la taille du système de fichiers ext4

df -h permet de vérifier le nouvel espace disponible
### 3.4 Configuration d’un proxy (Squid)

Un serveur proxy est un serveur intermédiaire entre les clients et Internet.  
Il permet de contrôler les accès réseau, d’améliorer la sécurité, de mettre en cache des contenus et de réduire la consommation de bande passante.

Dans ce TP, le proxy Squid utilisé est :
- **Nom :** proxy.ufr-info-p6.jussieu.fr  
- **Port :** 3128  
- **Protocoles :** HTTP / HTTPS

---

#### Configuration du proxy pour les outils en ligne de commande (wget, curl, etc.)

Pour rendre le proxy accessible à tous les utilisateurs du système, il est possible de définir des variables d’environnement dans le fichier `/etc/profile`.

```bash
export http_proxy="http://proxy.ufr-info-p6.jussieu.fr:3128"
export https_proxy="http://proxy.ufr-info-p6.jussieu.fr:3128"
export ftp_proxy="http://proxy.ufr-info-p6.jussieu.fr:3128"
export RSYNC_PROXY="proxy.ufr-info-p6.jussieu.fr:3128"
```
Pour les outils en ligne de commande, le proxy est défini via des variables d’environnement dans `/etc/profile`.  
Pour le gestionnaire de paquets APT, la configuration se fait dans `/etc/apt/apt.conf` afin de permettre les mises à jour à travers le proxy.
# TP 02 – Services, processus signaux
## 1 Secure Shell : SSH
### 1.1 Exercice : Connection ssh root (reprise fin tp-01)
#### Installation du service SSH

**Commande :**
```bash
apt search openssh-server
apt install openssh-server
```
**Explication :**
Le paquet openssh-server permet d’installer le service SSH afin d’autoriser les connexions distantes sécurisées vers la machine.
Configuration de SSH pour autoriser root avec mot de passe

Le fichier de configuration du serveur SSH est :

/etc/ssh/sshd_config

Les directives modifiées sont :
```bash
PermitRootLogin yes
PasswordAuthentication yes
```
#### Après modification :
```bash
systemctl restart ssh
```
#### Explication des options (man sshd_config)
PermitRootLogin

#### Options possibles :

    yes : autorise la connexion root par SSH

    no : interdit totalement la connexion root

    prohibit-password : autorise root uniquement par clé SSH

    forced-commands-only : autorise root seulement pour des commandes forcées

#### Avantages / Inconvénients :

    yes : simple mais peu sécurisé

    prohibit-password : recommandé (clé SSH uniquement)

    no : très sécurisé mais nécessite un autre compte administrateur

#### Choix 
yes, afin de valider la connexion root demandée dans l’exercice.
PasswordAuthentication

Options :

    yes : autorise l’authentification par mot de passe

    no : interdit l’authentification par mot de passe

#### Avantage :

    Simple à utiliser

#### Inconvénient :

    Vulnérable aux attaques par force brute

### 1.2 Exercice : Authentification par clef / G´en´eration de clefs
#### Génération du couple de clés sur la machine hôte

**Commande :**
```bash
ssh-keygen
```
**Explication :**
Lors de l’exécution de la commande `ssh-keygen`, OpenSSH génère par défaut une paire de clés utilisant l’algorithme **ED25519**.

Les fichiers générés sont :
- Clé privée : `~/.ssh/id_ed25519`
- Clé publique : `~/.ssh/id_ed25519.pub`
Une clé SSH permet une authentification forte sans mot de passe.
Dans un contexte réel, ne pas utiliser de passphrase est une mauvaise pratique car toute personne ayant accès à la clé privée pourrait se connecter au serveur

### 1.3 Authentification par clé – Connexion au serveur
#### Création du répertoire `.ssh` pour root

Sur le serveur, connecté en root :

```bash
mkdir -p /root/.ssh
```
**Explication :**
Le répertoire .ssh contient les fichiers de configuration SSH propres à l’utilisateur, notamment les clés autorisées pour la connexion.
#### Ajout de la clé publique dans authorized_keys

```bash
mkdir -p /root/.ssh
```
**Vérifier qu’il existe**
```bash
ls -ld /root/.ssh
```
**Résultat**
drwx------ 2 root root 4096 Jan 30 2026 /root/.ssh
