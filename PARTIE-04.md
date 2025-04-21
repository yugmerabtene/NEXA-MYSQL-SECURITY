# 🌐 **PARTIE 4 – Installation et sécurisation de WordPress**

---

## 📦 Étape 1 – Télécharger et installer WordPress

### 🔍 Objectif :
Installer WordPress dans le répertoire web sécurisé `/var/www/html/wordpress`

### 🖥️ Commandes :
```bash
cd /var/www/html
sudo wget https://wordpress.org/latest.tar.gz
sudo tar -xvzf latest.tar.gz
sudo rm latest.tar.gz
```

### 📁 Droits et permissions :
```bash
sudo chown -R www-data:www-data wordpress
sudo chmod -R 755 wordpress
```

---

## ⚙️ Étape 2 – Configurer la base de données

> Si ce n’est pas encore fait, créer une base de données dédiée :

```bash
sudo mysql -u root -p

CREATE DATABASE wordpress;
CREATE USER 'wp_user'@'localhost' IDENTIFIED BY 'MotDePasseUltraSecurise!';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wp_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

---

## 🛠️ Étape 3 – Créer et sécuriser le fichier `wp-config.php`

### Copier le fichier exemple :
```bash
cd /var/www/html/wordpress
sudo cp wp-config-sample.php wp-config.php
sudo nano wp-config.php
```

### Modifier les lignes suivantes :
```php
define( 'DB_NAME', 'wordpress' );
define( 'DB_USER', 'wp_user' );
define( 'DB_PASSWORD', 'MotDePasseUltraSecurise!' );
define( 'DB_HOST', 'localhost' );
```

### Générer les clés de sécurité :
Utiliser : https://api.wordpress.org/secret-key/1.1/salt/  
Copier-coller dans `wp-config.php` à la place des lignes existantes.

---

## 🔐 Étape 4 – Bonnes pratiques de sécurité `wp-config.php`

### 1. Déplacer le fichier à la racine du site
```bash
sudo mv /var/www/html/wordpress/wp-config.php /var/www/wp-config.php
```
WordPress le reconnaît automatiquement.

### 2. Restreindre les permissions
```bash
sudo chmod 640 /var/www/wp-config.php
```

---

## 🚫 Étape 5 – Bloquer l’édition des fichiers via le back-office

Dans `wp-config.php`, ajouter :
```php
define( 'DISALLOW_FILE_EDIT', true );
```

Cela empêche les modifications de thèmes/plugins depuis l’interface admin.

---

## 🔧 Étape 6 – Modifier le préfixe des tables MySQL

> Dans `wp-config.php`, change :
```php
$table_prefix = 'wp_';
```
Par exemple :
```php
$table_prefix = 'wp8s_';
```

⚠️ À faire **avant** l'installation via navigateur.

---

## 🧱 Étape 7 – Accès au site et configuration via navigateur

Visiter dans ton navigateur :
📲 `http://IP/wordpress`

Suivre l’installation de WordPress :
- Choisir la langue
- Renseigner les informations du site
- Créer un **compte admin fort** (nom + mot de passe fort !)

---

## 🛡️ Étape 8 – Sécuriser WordPress après installation

### 🔍 Objectif :
Appliquer les meilleures pratiques de sécurité post-installation.

### 📦 Plugins recommandés :
1. **Wordfence** : pare-feu applicatif + scanner de sécurité
2. **iThemes Security** : durcissement de la configuration
3. **Limit Login Attempts Reloaded** : anti-brute force
4. **UpdraftPlus** : sauvegardes régulières
5. **WP Hide Login** : changer l’URL `/wp-login.php`

---

## ☁️ Étape 9 – (Facultatif mais recommandé) : protection DDoS

- Créer un compte Cloudflare gratuit
- Ajouter ton domaine
- Activer **protection DNS + SSL + anti-bot**

---

## ✅ Résumé des sécurisations WordPress

| Élément                          | Sécurisation mise en place                          |
|----------------------------------|------------------------------------------------------|
| Base de données                  | Utilisateur unique, mot de passe fort               |
| `wp-config.php`                  | Droits réduits, déplacé, avec clés uniques          |
| Interface admin                  | `DISALLOW_FILE_EDIT`, plugins de sécurité           |
| Préfixe des tables               | Personnalisé (ex: `wp8s_`)                          |
| Plugins                          | Wordfence, iThemes, etc.                            |
| Accès                            | `/wp-login.php` masqué ou changé                    |
| Connexion                        | HTTPS activé avec SSL auto-signé                    |
