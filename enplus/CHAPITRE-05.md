# 🛡️ Chapitre 4 : Outils de Défense et Détection

---

## 🎯 Objectifs pédagogiques

À l’issue de ce chapitre, vous serez capable de :
- Comprendre les mécanismes de défense périmétrique et applicative
- Mettre en œuvre un **pare-feu applicatif (WAF)**
- Configurer **Fail2Ban** pour protéger contre les attaques par force brute
- Mettre en place une **surveillance via les logs** du serveur de base de données
- Détecter les comportements anormaux ou suspects sur les bases

---

## 🔐 1. Défense périmétrique : firewall système

### 🧱 Objectif :
Restreindre les accès à la base de données à des adresses IP autorisées uniquement.

### 🔧 Exemple avec `iptables` :
```bash
sudo iptables -A INPUT -p tcp --dport 3306 -s 192.168.1.100 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 3306 -j DROP
```

### 🔧 Exemple avec `ufw` (Ubuntu) :
```bash
sudo ufw allow from 192.168.1.100 to any port 3306
sudo ufw deny 3306
```

---

## 🌐 2. WAF (Web Application Firewall)

### 🔍 Définition :
Un **WAF** protège les applications web (et indirectement les bases de données) contre les attaques **au niveau HTTP/HTTPS**, notamment les injections SQL, XSS, etc.

### 🔧 Outil courant : **ModSecurity**
- Module pour Apache/Nginx
- Fonctionne avec OWASP Core Rule Set (CRS)

### 📦 Installation (Apache exemple) :
```bash
sudo apt install libapache2-mod-security2
sudo a2enmod security2
```

### 📄 Configuration :
- Fichier : `/etc/modsecurity/modsecurity.conf`
- Activez le mode **détection** ou **prévention**

---

## 🚫 3. Protection contre la force brute avec Fail2Ban

### 🔍 Fonctionnement :
- Surveille les fichiers de log à la recherche de **modèles d’échec de connexion répétés**
- Applique une **règle temporaire de bannissement IP** via `iptables`

### 📦 Installation :
```bash
sudo apt install fail2ban
```

### 📁 Configuration :
Créez un fichier `/etc/fail2ban/jail.d/mysql.conf` :
```ini
[mysql]
enabled  = true
port     = 3306
filter   = mysql-auth
logpath  = /var/log/mysql/error.log
maxretry = 5
bantime  = 600
```

> ✅ Cela bannit une IP pendant 10 minutes après 5 tentatives échouées.

### 📄 Exemple de filtre `/etc/fail2ban/filter.d/mysql-auth.conf`
```ini
[Definition]
failregex = Access denied for user .* from <HOST>
```

---

## 📝 4. Surveillance par les logs de base de données

### 🔍 Pourquoi ?
Permet de :
- Détecter des comportements anormaux
- Tracer les requêtes lentes ou inhabituelles
- Repérer une potentielle compromission

---

### 📄 a. MySQL – logs utiles
| Log                        | Utilité                                     |
|---------------------------|---------------------------------------------|
| `general_log`             | Toutes les requêtes                        |
| `slow_query_log`          | Requêtes lentes (> x sec)                  |
| `error_log`               | Connexions échouées, crashs, erreurs SQL   |
| `audit_log` (plugin)      | Journalisation fine des accès utilisateurs |

#### Exemple d’activation (dans `my.cnf`) :
```ini
[mysqld]
general_log = 1
general_log_file = /var/log/mysql/mysql.log
slow_query_log = 1
long_query_time = 2
```

---

### 📄 b. PostgreSQL – logs utiles
```conf
logging_collector = on
log_directory = 'log'
log_filename = 'postgresql.log'
log_connections = on
log_disconnections = on
log_duration = on
log_statement = 'mod'
```

---

## 🧠 5. Corrélation et alertes

### 🛠️ Intégration avec des outils de SIEM :
- **Wazuh**, **Graylog**, **Splunk**, ou **ELK stack**
- Centralisation des logs
- Détection automatique de comportements suspects
- Alerting (mail, Slack, Discord, etc.)

---

## ✅ Bonnes pratiques

- Garder les logs pendant **au moins 90 jours**
- Restreindre l’accès aux fichiers de logs (`chmod 600`)
- Mettre en place une **politique de rotation** (`logrotate`)
- Activer les logs dès la mise en production
- Tester régulièrement les déclencheurs d’alertes

---

## 🧪 Exercices pratiques

### 🧩 Exercice 1
Configurer `fail2ban` pour bannir une IP pendant 15 minutes après 3 échecs de connexion à MySQL.

> ✅ Fichier `mysql.conf` attendu :
```ini
[mysql]
enabled  = true
port     = 3306
filter   = mysql-auth
logpath  = /var/log/mysql/error.log
maxretry = 3
bantime  = 900
```

---

### 🧩 Exercice 2
Activer le journal des requêtes lentes sur MySQL pour tracer les requêtes dépassant 1,5 seconde.

> ✅ Configuration dans `my.cnf` :
```ini
[mysqld]
slow_query_log = 1
long_query_time = 1.5
```
