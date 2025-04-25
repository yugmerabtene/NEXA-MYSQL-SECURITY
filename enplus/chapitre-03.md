# 🛠️ Chapitre 3 : Configuration sécurisée du serveur de base de données

---

## 🎯 Objectifs pédagogiques

À la fin de ce chapitre, vous saurez :
- Sécuriser la configuration de MySQL ou PostgreSQL au niveau système
- Restreindre les accès réseaux à la base de données
- Protéger les accès physiques et logiques au serveur
- Appliquer les meilleures pratiques de sécurité serveur

---

## 🔐 1. Sécurité de base : notion de périmètre

### 📌 Enjeux :
- Limiter la **surface d’exposition** aux attaques
- Restreindre les **accès inutiles**
- Assurer l’intégrité et la confidentialité des données

> 🧠 Une base de données n’est pas sécurisée uniquement par son mot de passe : la configuration du serveur compte autant, voire plus.

---

## 🗂️ 2. Configuration sécurisée de MySQL (ou MariaDB)

Le fichier principal de configuration est souvent :
- Sous **Linux** : `/etc/mysql/my.cnf` ou `/etc/my.cnf`

### ⚙️ Paramètres essentiels

| Paramètre                    | Fonction                                                         |
|-----------------------------|------------------------------------------------------------------|
| `bind-address = 127.0.0.1`  | Empêche l’accès distant (accessible seulement en local) ✅         |
| `skip-name-resolve`         | Évite les résolutions DNS lentes et potentiellement dangereuses ✅ |
| `local-infile = 0`          | Désactive l’importation de fichiers locaux (contre SQLi avancée) ✅ |
| `secure-file-priv`          | Définit un dossier autorisé pour les exports/imports de fichiers ✅ |
| `symbolic-links = 0`        | Empêche les liens symboliques (limite certains exploits) ✅        |

### ✅ Exemple minimal dans `/etc/mysql/my.cnf`
```ini
[mysqld]
bind-address = 127.0.0.1
skip-name-resolve
local-infile = 0
secure-file-priv = "/var/lib/mysql-files"
symbolic-links = 0
```

---

## 🌐 3. Restreindre l’accès réseau

### 🔒 a. Éviter les connexions à distance
Si l’application est sur le **même serveur**, désactiver les accès distants :
```ini
bind-address = 127.0.0.1
```

Sinon, limiter l’accès aux IP autorisées via :

### 🔥 b. Pare-feu avec `iptables` ou `ufw`
#### Exemple : bloquer tout sauf l’IP 192.168.1.100 sur le port MySQL (3306)
```bash
sudo iptables -A INPUT -p tcp --dport 3306 ! -s 192.168.1.100 -j DROP
```

#### Avec UFW (Ubuntu) :
```bash
sudo ufw allow from 192.168.1.100 to any port 3306
sudo ufw deny 3306
```

---

## 🧑‍💻 4. Sécuriser les accès et les comptes

### 📛 a. Ne jamais utiliser `root` pour les applications
Créer un **utilisateur dédié avec droits limités**.

### 🧾 b. Désactiver les utilisateurs anonymes
```sql
DELETE FROM mysql.user WHERE User='';
FLUSH PRIVILEGES;
```

### 🔁 c. Changer le port par défaut (optionnel)
Changer le port 3306 ne bloque pas un attaquant, mais **ralentit les scanners automatiques** :
```ini
port = 3307
```

---

## 🧱 5. Sécurisation des fichiers systèmes

### 🔐 Droits d'accès Unix :
- Les fichiers de configuration (`/etc/mysql/`, `/var/lib/mysql/`) doivent être accessibles **uniquement par l’utilisateur mysql** :
```bash
sudo chown -R mysql:mysql /etc/mysql
sudo chmod -R 700 /etc/mysql
```

### 🔑 Protéger les fichiers de logs :
```bash
sudo chmod 600 /var/log/mysql/*.log
```

---

## 🛑 6. Accès physique et segmentation réseau

- **Héberger la base dans un VLAN** ou un sous-réseau isolé
- Accès SSH restreint à certains administrateurs via clé publique
- Utiliser un **VPN** pour l’accès distant
- Si possible, dédier un **serveur uniquement à la base** (pas d’app, pas de site dessus)

---

## ✅ Bonnes pratiques

- Modifier les mots de passe des comptes `root`, `admin` régulièrement
- Journaliser toutes les connexions (`general_log`, `slow_query_log`)
- Mettre à jour régulièrement le moteur de base (MySQL, MariaDB, PostgreSQL)
- Désactiver les fonctions inutiles (ex : `LOAD DATA LOCAL` si non utilisé)
- Tester les paramètres après chaque modification avec :
```bash
sudo systemctl restart mysql
```

---

## 🎓 Exercices pratiques

### 🧩 Exercice 1
Modifier la configuration pour désactiver l’accès distant et interdire l’importation de fichiers locaux.

> ✅ Fichier `my.cnf` attendu :
```ini
[mysqld]
bind-address = 127.0.0.1
local-infile = 0
```

---

### 🧩 Exercice 2
Configurer `iptables` pour autoriser le port 3306 uniquement à l’IP 192.168.10.20.

> ✅ Commande :
```bash
sudo iptables -A INPUT -p tcp --dport 3306 ! -s 192.168.10.20 -j DROP
```
