# 🔐 **CHAPITRE-01 – Sécurisation du Serveur Linux**

Cette partie vise à **préparer un serveur Linux** (Debian/Ubuntu) à l’hébergement de services web de manière **sécurisée**, avant toute installation de stack ou d’application, **et à intégrer un WAF (ModSecurity)** pour protéger les applications web.

---

## 🔧 Étape 1 – Mise à jour du système

### 🔍 Objectif :
S'assurer que tous les paquets installés sont à jour pour corriger les vulnérabilités connues.

### 🖥️ Commandes :
```bash
sudo apt update && sudo apt upgrade -y
sudo apt autoremove -y
```

---

## 👤 Étape 2 – Création d’un utilisateur non-root

### 🔍 Objectif :
Travailler sans le compte root pour limiter les dégâts en cas de compromission.

### 🖥️ Commandes :
```bash
sudo adduser devadmin
sudo usermod -aG sudo devadmin
```

> 🔐 Ce compte `devadmin` pourra utiliser `sudo` pour obtenir temporairement les droits root.

---

## 🔐 Étape 3 – Configuration SSH sécurisée (avec PuTTY/PuTTYgen)

### 🔍 Objectif :
Remplacer l'authentification par mot de passe par une **authentification par clé** + désactivation de root + changement de port SSH.

---

### 📥 Sur le poste **Windows** :

#### 1. **Génération de la paire de clés avec PuTTYgen** :
- Ouvre **PuTTYgen**
- Type de clé : RSA 4096 bits
- Clique sur **Generate**
- **Copie la clé publique**
- Sauvegarde la clé privée sous : `cle_privee_devadmin.ppk`

---

### 📤 Sur le **serveur Linux** :

#### 2. Ajouter la clé publique à l’utilisateur :
```bash
su - devadmin
mkdir -p ~/.ssh
nano ~/.ssh/authorized_keys
```

➡️ Colle ici la clé publique copiée depuis PuTTYgen

Puis :
```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

---

### 🔧 3. Modifier la configuration SSH :
```bash
sudo nano /etc/ssh/sshd_config
```

➡️ Modifier les lignes suivantes :
```ini
Port 2222
PermitRootLogin no
PasswordAuthentication no


si la connexion marche toujours sans clé chercher ça : 
grep -ir "PasswordAuthentication" /etc/ssh/sshd_config.d/
et mettre PasswordAuthentication no 
```

Puis :
```bash
sudo systemctl restart ssh
```

---

### ✅ Vérification :
1. Lancer **PuTTY**
2. Renseigner :
   - **Host** : `devadmin@ip_serveur`
   - **Port** : `2222`
   - **Auth** → charger la clé `.ppk`
3. Se connecter sans mot de passe

> ✅ Le port SSH est changé, le root est désactivé, et seule une clé permet la connexion.

---

## 🧱 Étape 4 – Configuration du pare-feu UFW (Uncomplicated Firewall)

### 🔍 Objectif :
Limiter les connexions aux ports indispensables uniquement.

### 🖥️ Commandes :
```bash
sudo apt install ufw -y

# Politique par défaut
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Autoriser les ports essentiels
sudo ufw allow 2222/tcp     # SSH
sudo ufw allow 80/tcp       # HTTP
sudo ufw allow 443/tcp      # HTTPS

# Activation
sudo ufw enable

# Vérification
sudo ufw status verbose
```

---

## 🚨 Étape 5 – Protection contre brute force avec Fail2Ban

### 🔍 Objectif :
Bloquer les adresses IP après plusieurs tentatives de connexion infructueuses.

### 🖥️ Commandes :
```bash
sudo apt install fail2ban -y
sudo systemctl enable fail2ban
```

Créer une config personnalisée :
```bash
sudo nano /etc/fail2ban/jail.local
```

Ajouter :
```ini
[sshd]
enabled = true
port = 2222
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
bantime = 3600
findtime = 600
```

Redémarrer le service :
```bash
sudo systemctl restart fail2ban
```

---

## 🛡️ Étape 6 – Installation d’un IDS : Suricata (détection d’intrusion)

### 🔍 Objectif :
Analyser le trafic réseau en temps réel pour détecter les comportements suspects ou malveillants.

### 🖥️ Commandes :
```bash
sudo apt install suricata -y
```

Activer et démarrer :
```bash
sudo systemctl enable suricata
sudo systemctl start suricata
```

Suricata surveille automatiquement les interfaces réseaux principales (`eth0`, `ens3`, etc).

---

## 🛡️ Étape 7 – Mise en place d’un WAF : **ModSecurity + OWASP Core Rule Set**

### 🔍 Objectif :
Protéger les applications web contre les attaques : injections SQL, XSS, LFI/RFI, etc.

---

### 📦 Installer ModSecurity pour Apache :
```bash
sudo apt install apache2 -y
sudo apt install libapache2-mod-security2 -y
```

Activer le module :
```bash
sudo a2enmod security2
sudo systemctl restart apache2
```

---

### 🔧 Activer ModSecurity en mode **de protection (ON)** :
```bash
sudo nano /etc/modsecurity/modsecurity.conf-recommended
```

Changer :
```ini
SecRuleEngine DetectionOnly
```
en
```ini
SecRuleEngine On
```

Puis enregistrer en tant que fichier actif :
```bash
sudo cp /etc/modsecurity/modsecurity.conf-recommended /etc/modsecurity/modsecurity.conf
```

---

### 📦 Ajouter les règles OWASP (CRS) :
```bash
cd /etc/modsecurity
sudo git clone https://github.com/coreruleset/coreruleset.git
cd coreruleset
sudo cp crs-setup.conf.example crs-setup.conf
```

Créer un lien vers les règles dans Apache :
```bash
sudo nano /etc/apache2/mods-enabled/security2.conf
```

Ajouter à la fin :
```apache
IncludeOptional /etc/modsecurity/coreruleset/crs-setup.conf
IncludeOptional /etc/modsecurity/coreruleset/rules/*.conf
```

✅ Solution recommandée :
Supprime ou commente cette ligne uniquement :
```apache
# IncludeOptional /usr/share/modsecurity-crs/*.load
```
Redémarrer Apache :
```bash
sudo systemctl restart apache2
```

---

## ✅ Récapitulatif des sécurisations mises en place

| Sécurité                                  | Statut              |
|-------------------------------------------|---------------------|
| 🔒 Utilisateur non-root                    | ✔️ `devadmin` créé |
| 🔐 Authentification par clé SSH            | ✔️ clé RSA 4096 bits |
| 🔄 Port SSH personnalisé                   | ✔️ port 2222       |
| 🚫 Accès root SSH désactivé                | ✔️ `PermitRootLogin no` |
| 🔥 Pare-feu UFW                            | ✔️ ports 80/443/2222 ouverts |
| 🚨 Protection brute-force (Fail2ban)       | ✔️ activée         |
| 🧠 IDS (Suricata)                          | ✔️ en surveillance réseau |
| 🛡️ WAF (ModSecurity + OWASP CRS)          | ✔️ activé et configuré |

---
## TP-01 : 
---

# 🧪 **TP Réseau – Mise en place d’une Interface Web de Supervision de Suricata avec Apache2**

## 🎯 Objectifs pédagogiques :
- Mettre en œuvre un système de **détection d’intrusion (IDS)** avec Suricata
- Activer et analyser les **logs d’alertes réseau en JSON**
- Déployer une interface web pour **visualiser en temps réel les alertes de sécurité**
- Intégrer l’interface à un serveur web **Apache2** pour un accès distant
- Protéger le système de supervision via **pare-feu**, **droits utilisateurs** et **bonnes pratiques**

---

## 📋 Consignes :

1. Le serveur fonctionne sous **Debian/Ubuntu** avec un compte non-root (`devadmin`).
2. Suricata est déjà installé et en cours d’exécution sur l’interface réseau (`ens3` ou `eth0`).
3. Le pare-feu `ufw` est actif, avec les ports essentiels ouverts.
4. Le port SSH est sécurisé sur `22 ou 2222 ou 1984 ou le port que vous aurez modifié` par clé publique.
5. Le serveur web **Apache2** est installé.
6. Vous devez :
   - Configurer Suricata pour écrire les logs d’alertes au format **EVE JSON**
   - Installer **Scirius Community Edition**
   - Intégrer Scirius via **Apache2 + mod_wsgi**
   - Accéder à l’interface web via un navigateur (HTTP)

---
