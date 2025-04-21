# 🔑 **PARTIE 5 – Gestion des mots de passe et des sauvegardes**

---

## 🔐 ÉTAPE 1 – Gestion des mots de passe

---

### 🔍 Objectif :
Utiliser des outils sécurisés pour **stocker**, **gérer**, et **protéger les secrets** : mots de passe SSH, MySQL, WordPress, certificats…

---

### ✅ Solution recommandée : **KeePassXC** (open source, local)

#### 📥 Installation (sur PC Linux ou Windows) :
- Windows : [https://keepassxc.org/download](https://keepassxc.org/download)
- Linux :
```bash
sudo apt install keepassxc -y
```

#### 📁 Créer une base `.kdbx` :
- Mot de passe principal fort (garde-le hors ligne aussi !)
- Classement par catégories : SSH, MySQL, WordPress, SSL…
- Ajoute les champs personnalisés : ports, IP, chemins fichiers clés

#### ✅ Bonnes pratiques :
- ⚠️ Ne jamais stocker les mots de passe en clair dans des fichiers `.txt`
- 🔐 Active la fermeture automatique de KeePassXC après 5 minutes d'inactivité
- 💾 Sauvegarde ton fichier `.kdbx` dans un coffre chiffré (clé USB GPG/Veracrypt)

---

## 💾 ÉTAPE 2 – Sauvegardes automatiques du serveur

---

### 🔍 Objectif :
Sauvegarder les **fichiers du site** et la **base de données** pour pouvoir restaurer rapidement après incident.

---

### 🗂️ a. Structure à sauvegarder

| Élément             | Localisation                         |
|---------------------|--------------------------------------|
| Site WordPress      | `/var/www/html/wordpress`            |
| wp-config.php       | `/var/www/wp-config.php`             |
| Base MySQL          | `wordpress` (utilisateur `wp_user`)  |
| Fichiers de config  | `/etc/apache2/`, `/etc/mysql/`, etc. |

---

### 🖥️ b. Script de sauvegarde de la base de données

```bash
#!/bin/bash

DATE=$(date +%F-%H%M)
BACKUP_DIR="/var/backups/mysql"
DB_NAME="wordpress"
DB_USER="wp_user"
DB_PASS="TonMotDePasse"

mkdir -p $BACKUP_DIR

mysqldump -u $DB_USER -p$DB_PASS $DB_NAME | gzip > $BACKUP_DIR/${DB_NAME}_$DATE.sql.gz
```

Rends-le exécutable :
```bash
chmod +x /usr/local/bin/backup_wpdb.sh
```

---

### 🗓️ c. Automatiser avec `cron`

Ouvre le planificateur de tâches :
```bash
crontab -e
```

Ajoute :
```cron
0 3 * * * /usr/local/bin/backup_wpdb.sh
```

📌 Cela exécutera la sauvegarde tous les jours à **03:00** du matin.

---

### 📁 d. Sauvegarde du site WordPress

#### Script de base :
```bash
#!/bin/bash

DATE=$(date +%F-%H%M)
SRC="/var/www/html/wordpress"
DEST="/var/backups/site"
mkdir -p $DEST

tar -czf $DEST/wp_site_$DATE.tar.gz $SRC
```

Planifie-le aussi avec `cron`.

---

## 🔐 ÉTAPE 3 – Chiffrer les sauvegardes (optionnel mais recommandé)

### 🔍 Objectif :
Empêcher toute personne ayant accès au serveur de lire les sauvegardes.

#### Méthode avec `gpg` :
```bash
gpg --output backup.sql.gz.gpg --symmetric backup.sql.gz
```

➡️ Utilise un mot de passe fort **différent** de tes autres accès

---

## 📂 ÉTAPE 4 – Restauration des sauvegardes (en cas de crash)

### a. Restaurer la base :
```bash
gunzip backup.sql.gz
mysql -u wp_user -p wordpress < backup.sql
```

### b. Restaurer le site :
```bash
tar -xvzf wp_site.tar.gz -C /var/www/html/
chown -R www-data:www-data /var/www/html/wordpress
```

---

## 🔁 ÉTAPE 5 – Sauvegarde "WordPress côté plugin" (optionnel)

### Plugin recommandé : **UpdraftPlus**

- Sauvegarde automatique du site, base, plugins, thèmes
- Restauration complète possible depuis le tableau de bord
- Connexion possible à : Google Drive, Dropbox, SFTP...

---

## ✅ Résumé des bonnes pratiques de cette partie

| Élément                         | Bonne pratique appliquée                             |
|----------------------------------|------------------------------------------------------|
| Mots de passe                   | Stockés dans KeePassXC `.kdbx` chiffré               |
| Scripts de backup               | Fichiers + base MySQL sauvegardés quotidiennement    |
| Automatisation                  | Tâches planifiées avec `cron`                        |
| Chiffrement                     | GPG appliqué aux sauvegardes sensibles               |
| Plugin WP (optionnel)           | UpdraftPlus pour version plugin de sauvegarde       |
