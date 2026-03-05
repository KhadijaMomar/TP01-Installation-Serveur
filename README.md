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
nano /root/ .ssh/authorized_keys
```
**Ajout des autorisations**
```bash
chmod 700 /root/ .ssh
```
**Vérifier qu’il existe**
```bash
ls -l /root/.ssh/authorized_keys
```
**Résultat**
rw------ 1 root root 93 2 févr. 11:39 authorized_keys 2026 
### 1.4 Exercice : Authentification par clef : depuis la machine hote

La connexion SSH au serveur testée par la clé correspondante
```bash
ssh -i /root/ .ssh/id_ed25519 root@127.0.0.1
```
**Résultat**
```bash
Linux debian 6.12.63+deb13-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.12.63-1 (2025-12-30) x86_64
Last login: Mon Feb  2 11:32:25 2026 from 127.0.0.1
root@debian:~#
```
**Conclusion**
La connexion s'effectue sans demande de mot passe, ce qui confirme que l'authentification SSH par clé publique est 
bien configurée.
### 1.5 Exercice : Sécurisation de l’accès SSH

#### Sécurisation de l’accès SSH pour root par clé uniquement
Afin d’éviter les tentatives d’authentification par brute-force sur le service SSH, l’accès distant au compte root a été sécurisé pour n’autoriser **que l’authentification par clé**.

La procédure consiste à modifier le fichier de configuration du démon SSH :
```bash
nano /etc/ssh/sshd_config
```
Les directives suivantes ont été configurées :
```bash
PermitRootLogin prohibit-password
PasswordAuthentication no
PubkeyAuthentication yes

```
PermitRootLogin prohibit-password : autorise la connexion root uniquement par clé publique.

PasswordAuthentication no : désactive complètement l’authentification par mot de passe.

PubkeyAuthentication yes : active l’authentification par clé publique.
**Une fois les modifications effectuées, le service SSH a été redémarré :**
```bash
systemctl restart ssh
```
**Résultat**
Les connexions root par mot de passe sont refusées, ce qui empêche toute tentative d’attaque automatisée basée sur des mots de passe.
#### Autres techniques de protection contre les attaques SSH
#### Changer le port SSH par défaut
Modifier le port SSH (22) réduit le nombre de scans automatisés.

Avantage : diminue le bruit et les attaques opportunistes.

Inconvénient : ne constitue pas une vraie protection contre une attaque ciblée.
#### Restriction par adresse IP

Limiter les connexions SSH à une liste d’adresses IP connues via un pare-feu (iptables / nftables).

Avantage : très sécurisé.

Inconvénient : peu flexible si l’administrateur se connecte depuis plusieurs réseaux.

#### Désactivation de l’accès SSH pour root

Créer un utilisateur administrateur classique et interdire complètement la connexion root distante.

Avantage : réduit fortement la surface d’attaque.

Inconvénient : nécessite une gestion supplémentaire des droits (sudo).
## 2 Processus
### 2.1 Exercice : Etude des processus UNIX
#### 1 - Affichage des processus avec la commande ps

Afin d’afficher tous les processus en cours d’exécution avec les informations demandées, j’ai utilisé la commande suivante :

```bash
ps -eo user,pid,%cpu,%mem,stat,start,time,command
```

#### TIME correspond au temps processeur total consommé par le processus depuis son lancement.

#### Pour identifier le processus ayant le plus utilisé le processeur, j’ai utilisé la commande :
```bash
ps -eo pid,user,%cpu,%mem,stat,start,time,command --sort=-%cpu | head
```
#### Sur ma machine, il s’agissait du processus top avec une utilisation de 0.7 % CPU, car l’analyse était en cours au moment de la mesure.
#### Le premier processus lancé est celui ayant le PID 1 :
```bash
ps -p 1 -o pid,command
```

***Résultat :***
```bash
PID COMMAND
1   /sbin/init
```

#### A quelle heure la machine a-t-elle démarré ?
***Commande utilisée :***

```bash
who -b
```
***Résultat :***
```bash
démarrage système 2026-02-16 10:33
```
#### Depuis combien de temps le serveur tourne-t-il ?

```bash
uptime
```
Cette commande affiche :

l’heure actuelle

le temps écoulé depuis le démarrage (uptime)

le nombre d’utilisateurs connectés

la charge système
***Résultat :***
```bash
11:34:13 up 1:30, 1 user, load average: 0,00 0,00 0,00
```

#### Nombre approximatif de processus créés depuis le boot
```bash
ps -e | wc -l
```
***Résultat :***
```bash
220
```
#### 2 -Processus père et PPID

#### Afficher le PPID
```bash
ps -eo pid,ppid,command
```
#### Liste ordonnée des ancêtres de la commande ps
```bash
ps -ejH
```
### 3 Utilisation de pstree

```bash
pstree -p
```
**Explication :**
Cette commande affiche l’arborescence complète des processus avec leurs PID.
Avantage : représentation plus claire et visuelle que ps.
### 4 Utilisation de top
```bash
top
```
**Explication :**
La commande top affiche les processus en temps réel, avec mise à jour périodique.

**Trier par occupation mémoire décroissante**
```bash
M
```
Cela trie les processus selon la mémoire résidente
**Processus le plus gourmand :**
Le processus le plus gourmand en ressources sur ma machine est systemd.
Il s’agit du processus d’initialisation du système (PID 1), lancé au démarrage par le noyau Linux.
Il est responsable du lancement et de la gestion des services système ainsi que de la supervision des processus.
Sa présence en tête de liste est normale, car il est constamment actif et coordonne l’ensemble du fonctionnement du système.
**Commandes interactives dans top:**

z : activer/désactiver l’affichage en couleur

R : inverser l’ordre de tri

O : choisir la colonne de tri

? : afficher l’aide

**Utilisation de htop**

Installation :
 ```bash
apt install htop
```
Commande :
```bash
htop
```
**Avantages de htop par rapport à top**

-Interface plus claire et colorée

-Navigation avec les flèches

-Sélection et arrêt de processus plus simple

-Tri plus intuitif (F6 pour choisir la colonne)

-Inconvénients

-Non installé par défaut

-Consomme légèrement plus de ressources

---

## 3 Arret d’un processus
### Création des scripts

#### Fichier date.sh
**Explication des scripts**

#!/bin/sh : indique l’interpréteur utilisé.

while true : boucle infinie.

sleep 1 : pause d’une seconde.

echo -n : affiche sans retour à la ligne.

date +%T : affiche l’heure au format HH:MM:SS.

date --date '5 hour ago' : affiche l’heure 5 heures en arrière.

Ces scripts simulent deux horloges s’exécutant en continu.

--- 

## 4 Les tubes
### Différence entre tee et cat
-cat lit une entrée et l’affiche sur la sortie standard.

-tee lit une entrée, l’affiche ET l’enregistre dans un fichier.

---

**Explication des commandes**
 ```bash
$ ls | cat
 ```
Affiche simplement le contenu du répertoire.
 ```bash
$ ls -l | cat > liste
 ```
Liste détaillée des fichiers

Redirige la sortie vers le fichier "liste"

Rien ne s’affiche à l’écran
 ```bash
$ ls -l | tee liste
 ```
Affiche le résultat à l’écran

Enregistre également dans le fichier "liste"
 ```bash
$ ls -l | tee liste | wc -l
 ```
Affiche la liste

L’enregistre dans "liste"

Compte le nombre de lignes (nombre d’éléments)

---

## 5 Journal système rsyslog
### Avant installation :

La commande dpkg -l | grep rsyslog ne retourne aucun résultat, ce qui signifie que le service rsyslog n’était pas installé sur le système.

---

### Après installation :

J’ai installé le service avec la commande apt install rsyslog.
Après installation, la commande systemctl status rsyslog indique que le service est actif (running) avec le PID [1019].

---

### Les messages standards sont généralement écrits dans :

/var/log/syslog

ou

/var/log/messages

Vérification à l'aide de cette commande :
 ```bash
cat /var/log/syslog
 ```
### Service cron

Le service cron permet de planifier l’exécution automatique de tâches à des horaires définis.

Exemple : sauvegardes automatiques, scripts planifiés, mises à jour.

---

### Commande tail -f
 ```bash
tail -f /var/log/messages
 ```
Cette commande affiche en temps réel les nouvelles lignes ajoutées au fichier.

Si l’on redémarre cron dans un autre terminal :
 ```bash
systemctl restart cron
 ```
On observe un message indiquant :

l’arrêt du service

puis son redémarrage

Cela confirme que rsyslog enregistre les événements système.

---

### Fichier /etc/logrotate.conf

Ce fichier permet de gérer la rotation des journaux :

Compression des anciens logs

Suppression après un certain délai

Limitation de taille

Archivage automatique

Cela évite que les fichiers journaux saturent l’espace disque.

---

### Commande dmesg
 ```bash
dmesg
 ```
Affiche les messages du noyau Linux au démarrage.

On peut y identifier :

Le modèle du processeur détecté

Les cartes réseau reconnues

Les périphériques matériels

Exemple typique :

CPU : Intel(R) Core(TM) i5-8250U

Carte réseau : Intel Ethernet Connection I219-V

---

# TP 03 – Shell bash

## Exercice1 : paramètres

### Script : analyse.sh
 ```bash
#!/bin/bash

echo "Bonjour, vous avez rentré $# paramètres."
echo "Le nom du script est $0"
echo "Le 3ème paramètre est $3"
echo "Voici la liste des paramètres : $@"
 ```

**Test :**
 ```bash
chmod +x analyse.sh
./analyse.sh a b c d
```

## Exercice 2 :  vérification du nombre de paramètres

### Script : concat.sh
 ```bash
#!/bin/bash

if [ $# -ne 2 ]; then
    echo "Erreur : vous devez entrer exactement 2 paramètres."
    exit 1
fi

CONCAT="$1$2"
echo "Résultat : $CONCAT"
 ```
## Exercice 3 : argument type et droits

### Script : test-fichier.sh
 ```bash
#!/bin/bash

if [ $# -ne 1 ]; then
    echo "Usage : $0 fichier"
    exit 1
fi

FIC="$1"

if [ ! -e "$FIC" ]; then
    echo "Le fichier $FIC n'existe pas."
    exit 1
fi

if [ -d "$FIC" ]; then
    echo "Le fichier $FIC est un répertoire."
elif [ -f "$FIC" ]; then
    echo "Le fichier $FIC est un fichier ordinaire."
fi

if [ -r "$FIC" ]; then
    echo "Accessible en lecture"
fi

if [ -w "$FIC" ]; then
    echo "Accessible en écriture"
fi

if [ -x "$FIC" ]; then
    echo "Accessible en exécution"
fi
 ```

 ## Exercice 4 : Afficher le contenu d’un répertoire

### Script : listedir.sh
 ```bash
#!/bin/bash

if [ $# -ne 1 ]; then
    echo "Usage : $0 repertoire"
    exit 1
fi

DIR="$1"

if [ ! -d "$DIR" ]; then
    echo "Ce n'est pas un répertoire."
    exit 1
fi

echo "####### fichiers dans $DIR/"
for f in "$DIR"/*; do
    if [ -f "$f" ]; then
        echo "$f"
    fi
done

echo "####### repertoires dans $DIR/"
for f in "$DIR"/*; do
    if [ -d "$f" ]; then
        echo "$f"
    fi
done
 ```
 ## Exercice 5 : Lister les utilisateurs

### Script : listUsers.sh
 ```bash
#!/bin/bash

cut -d: -f1,3 /etc/passwd | while IFS=: read login uid
do
    if [ "$uid" -gt 100 ]; then
        echo "$login"
    fi
done
 ```
## Exercice 6 : Mon utilisateur existe t’il
### Script : verifUser.sh
 ```bash
#!/bin/bash

if [ $# -ne 1 ]; then
    echo "Usage : $0 login"
    exit 1
fi

LOGIN="$1"

RESULT=$(grep "^$LOGIN:" /etc/passwd)

if [ -n "$RESULT" ]; then
    echo "$RESULT" | cut -d: -f3
fi
```
## Exercice 7: Creation utilisateur
### Script : creation-utilisateur.sh
 ```bash
 #!/bin/bash

# Vérifier que l'utilisateur est root
if [ "$USER" != "root" ]; then
    echo "Erreur : ce script doit être exécuté par root."
    exit 1
fi

echo -n "Login : "
read LOGIN

echo -n "Nom : "
read NOM

echo -n "Prenom : "
read PRENOM

echo -n "UID : "
read UID

echo -n "GID : "
read GID

echo -n "Commentaires : "
read COMMENT

if grep -q "^$LOGIN:" /etc/passwd ; then
    echo "Erreur : l'utilisateur $LOGIN existe déjà."
    exit 1
fi

HOME_DIR="/home/$LOGIN"

if [ -d "$HOME_DIR" ]; then
    echo "Erreur : le répertoire $HOME_DIR existe déjà."
    exit 1
fi

# Création de l'utilisateur
useradd -u "$UID" -g "$GID" -c "$PRENOM $NOM $COMMENT" -m -d "$HOME_DIR" "$LOGIN"


if [ $? -eq 0 ]; then
    echo "Utilisateur $LOGIN créé avec succès."
else
    echo "Erreur lors de la création de l'utilisateur."
fi
 ```
## Exercice 8: Lecture au clavier
### Script : readFile.sh
 ```bash
#!/bin/bash

if [ $# -ne 1 ]; then
    echo "Usage : $0 repertoire"
    exit 1
fi

for f in "$1"/*
do
    if file "$f" | grep -q "text"; then
        echo -n "Voulez-vous visualiser le fichier $f ? (o/n) "
        read REP
        if [ "$REP" = "o" ]; then
            more "$f"
        fi
    fi
done
```
## Exercice 9 : Appréciation
### Script : appreciation.sh
 ```bash
#!/bin/bash

while true
do
    echo -n "Entrez une note (ou q pour quitter) : "
    read NOTE

    if [ "$NOTE" = "q" ]; then
        break
    fi

    if [ "$NOTE" -ge 16 ] && [ "$NOTE" -le 20 ]; then
        echo "Très bien"
    elif [ "$NOTE" -ge 14 ]; then
        echo "Bien"
    elif [ "$NOTE" -ge 12 ]; then
        echo "Assez bien"
    elif [ "$NOTE" -ge 10 ]; then
        echo "Moyen"
    else
        echo "Insuffisant"
    fi
done
 ```
