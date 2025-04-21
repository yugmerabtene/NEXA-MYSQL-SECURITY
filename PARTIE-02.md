# 🧱 **PARTIE 2 – Installation Sécurisée du Stack LAMP**

> Le stack **LAMP** (Linux, Apache, MySQL, PHP) est une base robuste pour héberger des sites comme WordPress. Dans cette partie, on installe **chaque composant** avec une **configuration renforcée**.

---

## 📦 Étape 1 – Installation d’Apache, PHP et MySQL

### 🔍 Objectif :
Disposer d’un serveur HTTP avec support PHP et base de données MySQL/MariaDB.

### 🖥️ Commandes :
```bash
sudo apt update
sudo apt install apache2 mysql-server php php-mysql libapache2-mod-php php-cli -y
```

---

## ⚙️ Étape 2 – Activation des services

```bash
sudo systemctl enable apache2
sudo systemctl enable mysql
```

### Vérifier que tout fonctionne :
```bash
systemctl status apache2
systemctl status mysql
```

---

## 🧽 Étape 3 – Sécurisation de MySQL

### 🔍 Objectif :
Supprimer les utilisateurs anonymes, désactiver l’accès root à distance, forcer le mot de passe root, supprimer les bases de tests.

### 🖥️ Commande :
```bash
sudo mysql_secure_installation
```

#### Réponses conseillées :
- **VALIDATE PASSWORD PLUGIN** : `Yes` (optionnel)
- **Change root password** : `Yes`
- **Remove anonymous users** : `Yes`
- **Disallow root login remotely** : `Yes`
- **Remove test database** : `Yes`
- **Reload privilege tables** : `Yes`

---

## 🧑‍💻 Étape 4 – Création d’un utilisateur MySQL spécifique

> On ne travaille **jamais** avec le compte root MySQL pour une application. On crée un utilisateur dédié, avec **accès limité à une seule base**.

### 🖥️ Commandes :
```bash
sudo mysql -u root -p
```

```sql
CREATE DATABASE wordpress;
CREATE USER 'wp_user'@'localhost' IDENTIFIED BY 'MotDePasseUltraSecurise!';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wp_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

---

## 🧠 Bonnes pratiques supplémentaires (à appliquer dès maintenant ou plus tard)

### 🔒 1. **Activer l’audit MySQL (facultatif pour production avancée)** :
```bash
INSTALL PLUGIN audit_log SONAME 'audit_log.so';
```

### 🔐 2. **Forcer la liaison MySQL uniquement sur localhost**
```bash
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```

Changer :
```ini
bind-address = 127.0.0.1
```

Puis :
```bash
sudo systemctl restart mysql
```

---

## 🖼️ Étape 5 – Tester Apache + PHP

### Créer un fichier de test :
```bash
sudo nano /var/www/html/info.php
```

Contenu :
```php
<?php
phpinfo();
?>
```

Puis visiter dans le navigateur :  
📲 `http://<IP_serveur>/info.php`

Si la page PHP s’affiche, **Apache et PHP fonctionnent correctement**.

> ❗ N’oublie pas de **supprimer `info.php`** après le test :
```bash
sudo rm /var/www/html/info.php
```

---

## ✅ Résumé des sécurisations de la partie 2

| Composant     | Sécurisation appliquée                          |
|---------------|--------------------------------------------------|
| Apache        | Config par défaut, PHP intégré                  |
| MySQL         | Mot de passe root, suppression comptes anonymes |
| Utilisateurs  | Un utilisateur MySQL par base                   |
| Services      | Activés au démarrage, testés                    |
| Liaison MySQL | `127.0.0.1` uniquement                          |

---

## 🔐 Bonus sécurité supplémentaire possible :

- 🔒 Utiliser **MariaDB** à la place de MySQL (plus léger, compatible, open)
- 🔁 Sauvegarder automatiquement les bases (`mysqldump`, `automysqlbackup`)
- 📥 Configurer un service de monitoring (Zabbix, Prometheus)
- 📊 Activer les logs détaillés Apache + audit MySQL
- 🔐 Chiffrer les fichiers de config contenant des mots de passe avec `gpg`
