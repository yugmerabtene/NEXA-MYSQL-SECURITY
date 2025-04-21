# 🔐 **PARTIE 3 – Sécurisation des services web**

---

## ⚙️ Étape 1 – Installation et configuration de phpMyAdmin

### 🔍 Objectif :
Permettre une gestion visuelle de MySQL, mais **sécurisée**.

### 🖥️ Commandes :
```bash
sudo apt install phpmyadmin -y
```

Pendant l’installation :
- Choisir `apache2` comme serveur web
- Laisser le système configurer automatiquement la base
- Définir un mot de passe sécurisé pour `phpmyadmin`

Activer manuellement si besoin :
```bash
sudo ln -s /etc/phpmyadmin/apache.conf /etc/apache2/conf-available/phpmyadmin.conf
sudo a2enconf phpmyadmin
sudo systemctl reload apache2
```

---

## 🔐 Étape 2 – Changer l’alias URL par défaut

> Par défaut, phpMyAdmin est accessible à `http://IP/phpmyadmin`, ce qui est trop facile à deviner.

### 🔍 Objectif :
Changer l’URL d’accès à une valeur personnalisée.

### 🖥️ Commandes :
```bash
sudo nano /etc/apache2/conf-available/phpmyadmin.conf
```

Modifier cette ligne :
```apache
Alias /phpmyadmin /usr/share/phpmyadmin
```

Par exemple :
```apache
Alias /adminsql /usr/share/phpmyadmin
```

Puis :
```bash
sudo systemctl reload apache2
```

📲 Ton phpMyAdmin est maintenant accessible via :  
`http://IP/adminsql`

---

## 🔒 Étape 3 – Activer `.htaccess` pour ajouter une protection par mot de passe

### a. Activer les `.htaccess` dans Apache

Éditer le fichier :
```bash
sudo nano /etc/apache2/apache2.conf
```

Trouver cette section :
```apache
<Directory /var/www/>
    AllowOverride None
```

Et la remplacer par :
```apache
<Directory /var/www/>
    AllowOverride All
```

Puis :
```bash
sudo systemctl restart apache2
```

---

### b. Créer la protection avec `.htaccess` et `.htpasswd`

#### 1. Créer un fichier `.htpasswd`
```bash
sudo apt install apache2-utils
sudo htpasswd -c /etc/apache2/.htpasswd adminsql
```

➡️ Saisis un mot de passe fort

#### 2. Créer un fichier `.htaccess` dans le dossier de phpMyAdmin
```bash
sudo nano /usr/share/phpmyadmin/.htaccess
```

Contenu :
```apache
AuthType Basic
AuthName "Zone protégée"
AuthUserFile /etc/apache2/.htpasswd
Require valid-user
```

---

## 🔏 Étape 4 – Activer HTTPS avec un **certificat SSL auto-signé**

> Pour chiffrer les échanges, même en environnement de test.

### 🖥️ Générer un certificat auto-signé :
```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
-keyout /etc/ssl/private/apache-selfsigned.key \
-out /etc/ssl/certs/apache-selfsigned.crt
```

➡️ Renseigne les champs demandés (CN = IP du serveur)

---

### 🔧 Configuration Apache SSL

Éditer :
```bash
sudo nano /etc/apache2/sites-available/default-ssl.conf
```

Vérifie ou ajoute :
```apache
SSLEngine on
SSLCertificateFile /etc/ssl/certs/apache-selfsigned.crt
SSLCertificateKeyFile /etc/ssl/private/apache-selfsigned.key
```

Activer le module SSL et le site :
```bash
sudo a2enmod ssl
sudo a2ensite default-ssl
sudo systemctl reload apache2
```

---

### (Optionnel) Rediriger HTTP vers HTTPS :
Dans le fichier de configuration du VirtualHost par défaut (`000-default.conf`) :
```bash
sudo nano /etc/apache2/sites-available/000-default.conf
```

Ajouter dans le `<VirtualHost *:80>` :
```apache
RewriteEngine On
RewriteCond %{HTTPS} off
RewriteRule ^(.*)$ https://%{HTTP_HOST}$1 [R=301,L]
```

Activer le module rewrite :
```bash
sudo a2enmod rewrite
sudo systemctl restart apache2
```

---

## 🔐 Étape 5 – Sécuriser Apache avec des headers HTTP (bonus sécurité)

### Ajouter des en-têtes de sécurité :
```bash
sudo nano /etc/apache2/conf-available/security.conf
```

Ajouter :
```apache
Header always set X-Frame-Options "SAMEORIGIN"
Header always set X-XSS-Protection "1; mode=block"
Header always set X-Content-Type-Options "nosniff"
Header always set Content-Security-Policy "default-src 'self';"
```

Activer le module headers :
```bash
sudo a2enmod headers
sudo systemctl reload apache2
```

---

## ✅ Résumé des sécurisations web

| Élément                  | Sécurisation mise en place                        |
|--------------------------|----------------------------------------------------|
| phpMyAdmin               | Accès via alias personnalisé (`/adminsql`)        |
| Apache                   | `.htaccess` activés                               |
| phpMyAdmin               | Protégé par mot de passe HTTP (`.htpasswd`)       |
| HTTPS                    | Certificat SSL auto-signé                         |
| Headers                  | Ajout de protections anti-XSS/Clickjacking        |
| HTTP                     | Redirection vers HTTPS (optionnel mais recommandé) |
