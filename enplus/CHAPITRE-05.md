# 💉 Chapitre 5 : Prévention des Injections SQL

---

## 🎯 Objectifs pédagogiques

À l’issue de ce chapitre, vous serez capable de :
- Comprendre le fonctionnement d’une injection SQL (SQLi)
- Identifier les points d’entrée vulnérables
- Implémenter des contre-mesures efficaces
- Utiliser des requêtes préparées pour sécuriser les interactions avec la base

---

## 🚨 1. Qu’est-ce qu’une injection SQL (SQLi) ?

### 🔍 Définition :
Une **injection SQL** est une attaque dans laquelle un utilisateur malveillant insère du code SQL dans un champ de saisie pour **manipuler la requête initiale**, souvent à des fins de :
- Vol de données
- Suppression ou modification de données
- Accès non autorisé
- Élévation de privilèges

### ⚠️ Exemple simple :
```sql
SELECT * FROM users WHERE username = '$username' AND password = '$password';
```
Si `$username = admin' --`  
Alors la requête devient :
```sql
SELECT * FROM users WHERE username = 'admin' --' AND password = '';
```
→ Authentification contournée

---

## 🎯 2. Points d’entrée vulnérables

- Formulaires de connexion
- Moteurs de recherche
- Paramètres d’URL
- Headers HTTP (User-Agent, Cookie, etc.)
- APIs REST ou GraphQL mal protégées

---

## 🔐 3. Techniques de prévention

### ✅ a. Requêtes préparées (paramétrées)

#### 🔧 En PHP avec PDO :
```php
$stmt = $pdo->prepare("SELECT * FROM users WHERE username = :username AND password = :password");
$stmt->execute(['username' => $u, 'password' => $p]);
```

#### 🔧 En Python avec `psycopg2` (PostgreSQL) :
```python
cursor.execute("SELECT * FROM users WHERE username=%s AND password=%s", (u, p))
```

#### ✅ Avantages :
- Séparation **code/données**
- Automatique escaping des caractères
- Inutile de filtrer manuellement chaque caractère dangereux

---

### ✅ b. Validation et filtrage des entrées

- Vérification des types (`int`, `email`, etc.)
- Utilisation de **regex strictes**
- Rejet de caractères interdits (`--`, `;`, `/* */`, etc.)
- Listes blanches plutôt que listes noires

#### Exemple : regex simple pour un identifiant alphanumérique
```php
if (!preg_match('/^[a-zA-Z0-9_]{4,20}$/', $_POST['username'])) {
  die('Entrée invalide.');
}
```

---

### ✅ c. Limitation des privilèges SQL

- Ne jamais utiliser un utilisateur `root` ou `admin` pour l'application
- Créer un compte limité (`SELECT`, `INSERT`, pas de `DROP`, `ALTER`)
- Appliquer le **principe du moindre privilège** (Chapitre 2)

---

### ✅ d. Journalisation des activités suspectes

- Logger toutes les requêtes en erreur ou suspectes
- Intégrer les logs SQL dans un SIEM (Wazuh, ELK)

---

## 🧬 4. Types d’injections SQL à connaître

| Type                          | Description                                 |
|-------------------------------|---------------------------------------------|
| **Classic SQLi**              | Injection directe dans la requête           |
| **Blind SQLi**                | Réponses vraies/faux sans message d'erreur  |
| **Time-Based Blind SQLi**     | Exploite les délais d’exécution             |
| **Union-Based SQLi**          | Combine plusieurs requêtes avec `UNION`     |
| **Error-Based SQLi**          | Exploite les messages d'erreurs retournés   |

---

## 🔧 5. Protection côté serveur web

- **Désactiver l'affichage des erreurs SQL** en production
- Utiliser un **WAF** (ModSecurity) pour filtrer les patterns SQLi
- Filtrer les entêtes HTTP (ex : `User-Agent`, `Referer`) si réutilisés dans la base

---

## ✅ Bonnes pratiques

- Utiliser systématiquement des requêtes préparées
- Ne jamais concaténer une entrée utilisateur dans une requête SQL
- Analyser les logs à la recherche de motifs suspects (`1=1`, `' OR ''='`)
- Protéger les points d’entrée API ou formulaires via **CSRF token** et **CAPTCHA**
- Faire des **tests de pénétration réguliers** (ex : avec sqlmap)

---

## 🧪 Exercices pratiques

### 🧩 Exercice 1
Explique pourquoi ce code est vulnérable :
```php
$sql = "SELECT * FROM users WHERE email = '$_POST[email]'";
```

> ✅ Réponse attendue :
Entrée utilisateur injectée directement dans la requête. Une personne pourrait entrer `test@ex.com' OR '1'='1` pour contourner la logique.

---

### 🧩 Exercice 2
Corrige cette requête en utilisant PDO :
```php
$sql = "SELECT * FROM users WHERE login = '$login' AND password = '$pass'";
```

> ✅ Solution :
```php
$stmt = $pdo->prepare("SELECT * FROM users WHERE login = :login AND password = :password");
$stmt->execute(['login' => $login, 'password' => $pass]);
```

---

### 🧩 Exercice 3
Testez un champ vulnérable d'une application de test (ex : DVWA ou bWAPP) avec :
```
' OR 1=1 --
```

> ✅ Observer si la requête retourne tous les utilisateurs.
