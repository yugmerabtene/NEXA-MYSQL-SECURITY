# 🔐 Chapitre 6 : Chiffrement et Hachage des Données

---

## 🎯 Objectifs pédagogiques

À l’issue de ce chapitre, vous serez capable de :
- Faire la distinction entre **chiffrement** et **hachage**
- Connaître les algorithmes modernes utilisés
- Chiffrer les données sensibles dans une base de données
- Hacher et saler correctement les mots de passe
- Appliquer des bonnes pratiques de gestion des secrets

---

## 🧠 1. Chiffrement vs Hachage : comprendre la différence

| Propriété       | Chiffrement                            | Hachage                                   |
|------------------|----------------------------------------|-------------------------------------------|
| Réversible ?     | ✅ Oui (avec la clé)                   | ❌ Non (fonction unidirectionnelle)        |
| Usage principal  | Protéger des données à déchiffrer     | Stocker en toute sécurité un secret       |
| Clé requise ?    | ✅ Oui                                  | ❌ Non                                     |
| Exemples         | AES, RSA                               | SHA-256, bcrypt, Argon2                   |

---

## 🏦 2. Chiffrement des données sensibles

### 🔐 a. Pourquoi chiffrer ?
- En cas de vol de base de données, les données restent **illisibles**
- Conforme au **RGPD** (art. 32 – sécurité des données)
- Utilisé pour : numéro de CB, informations médicales, adresses, etc.

---

### 🔧 b. Algorithmes de chiffrement courants
- **AES (Advanced Encryption Standard)** : rapide, sécurisé (128, 192, 256 bits)
- **RSA** : asymétrique, utilisé pour l’échange sécurisé de clés
- **ChaCha20** : alternatif moderne, très rapide

---

### 🧪 Exemple : chiffrement AES avec PHP
```php
$cle = openssl_random_pseudo_bytes(32);
$iv = openssl_random_pseudo_bytes(16);
$texte_chiffre = openssl_encrypt("1234567890123456", "aes-256-cbc", $cle, 0, $iv);
```

---

### 🛠️ c. Chiffrement en base MySQL

MySQL dispose de fonctions intégrées :
```sql
-- Chiffrement
SELECT AES_ENCRYPT('secret', 'ma_cle');

-- Déchiffrement
SELECT AES_DECRYPT(AES_ENCRYPT('secret', 'ma_cle'), 'ma_cle');
```

> ⚠️ **À éviter en production** : préférez le chiffrement côté application pour plus de contrôle et de sécurité.

---

## 🔐 3. Hachage des mots de passe

### 🔍 Pourquoi hacher ?
- Un mot de passe **ne doit jamais être stocké en clair**
- Un bon hachage rend **irréaliste toute tentative de brute-force**

---

### ⚙️ Algorithmes recommandés

| Algorithme | Niveau sécurité | Remarques                          |
|------------|------------------|------------------------------------|
| **bcrypt** | ⭐⭐⭐⭐              | Référence moderne, adaptable       |
| **Argon2** | ⭐⭐⭐⭐⭐             | Le plus recommandé (OWASP 2024)    |
| **SHA-256** | ⭐⭐               | À éviter sans salage               |

---

### 🔧 Exemple avec PHP (bcrypt) :
```php
$hash = password_hash("monMotDePasse", PASSWORD_BCRYPT);
```

Pour vérifier :
```php
if (password_verify("monMotDePasse", $hash)) {
    echo "Mot de passe OK";
}
```

---

## 🧂 4. Le salage des hashs

### 🔍 Définition :
Un **sel (salt)** est une chaîne aléatoire ajoutée au mot de passe avant le hachage.

> Objectif : empêcher les attaques par **table de correspondance (rainbow table)**

### 💡 Exemple :
```php
$salt = bin2hex(random_bytes(16));
$hash = hash('sha256', $salt . $password);
```

> ⚠️ En pratique, utiliser **bcrypt ou Argon2** qui salent automatiquement et gèrent les itérations.

---

## 🔑 5. Gestion des clés de chiffrement

### 📁 Bonnes pratiques :
- Ne **jamais stocker la clé dans la même base** que les données chiffrées
- Utiliser un **gestionnaire de secrets** : HashiCorp Vault, AWS KMS, Azure Key Vault
- Restreindre les accès aux fichiers contenant des clés (`chmod 600`)
- Renouveler les clés régulièrement

---

## ✅ Bonnes pratiques générales

- Toujours **hacher les mots de passe** avec un algorithme fort (bcrypt, Argon2)
- Chiffrer les données sensibles côté application
- Séparer les rôles : **base ≠ application ≠ clé**
- Activer le chiffrement **des sauvegardes**
- Ne jamais afficher d'information sur un hash/décryptage raté

---

## 🧪 Exercices pratiques

### 🧩 Exercice 1
Stocker un mot de passe de manière sécurisée avec bcrypt et vérifier l’identifiant utilisateur.

> ✅ En PHP :
```php
$password = "Secret123";
$hash = password_hash($password, PASSWORD_BCRYPT);
password_verify("Secret123", $hash); // true
```

---

### 🧩 Exercice 2
Chiffrer un numéro de carte bancaire avec AES-256 en PHP.

> ✅ Attendu : chiffrement côté application avec clé/IV stockés séparément

---

### 🧩 Exercice 3
Expliquer pourquoi il ne faut pas stocker cette ligne en base :
```sql
INSERT INTO users (email, password) VALUES ('a@a.com', '123456');
```

> ✅ Réponse attendue : mot de passe en clair, très vulnérable au vol de la base
