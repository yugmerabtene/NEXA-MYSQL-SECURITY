# 🧩 Chapitre 2 : Le Principe du Moindre Privilège

---

## 🎯 Objectifs pédagogiques

À l’issue de ce chapitre, vous serez capable de :
- Comprendre et appliquer le **principe du moindre privilège** dans un système de base de données
- Utiliser le modèle **RBAC (Role-Based Access Control)** pour structurer les autorisations
- Créer, attribuer et retirer des droits aux utilisateurs via les commandes SQL : `CREATE USER`, `GRANT`, `REVOKE`

---

## 🛡️ 1. Le principe du moindre privilège

### 🔍 Définition
Le **principe du moindre privilège** stipule qu’un utilisateur ou un programme ne doit disposer que des **droits strictement nécessaires** à l’exécution de ses fonctions – ni plus, ni moins.

### 🎯 Objectif
Réduire la **surface d’attaque** et **limiter les dégâts potentiels** en cas de compromission d’un compte.

### 💡 Exemple concret
Un analyste doit uniquement consulter les données, pas les modifier. Lui accorder un droit `SELECT` uniquement sur une base est donc plus sûr qu’un `ALL PRIVILEGES`.

---

## 🧱 2. Le modèle RBAC : Role-Based Access Control

### 🏗️ Définition
Le RBAC (contrôle d’accès basé sur les rôles) est un **modèle d’organisation des permissions** où les utilisateurs reçoivent des rôles qui regroupent un ensemble de privilèges.

### 📌 Avantages
- Centralisation des autorisations
- Facilité d’attribution des droits
- Réduction des erreurs de configuration
- Sécurité accrue

### 🧭 Exemple de rôles
| Rôle         | Privilèges                    |
|--------------|-------------------------------|
| `reader`     | `SELECT` uniquement           |
| `editor`     | `SELECT`, `INSERT`, `UPDATE`  |
| `admin`      | `ALL PRIVILEGES`              |

---

## 🔧 3. Gestion des utilisateurs et des privilèges

### 🔨 a. Créer un utilisateur
```sql
CREATE USER 'analyste1'@'localhost' IDENTIFIED BY 'motDePasseFort!';
```

- `'analyste1'@'localhost'` : nom d'utilisateur et origine des connexions autorisées
- `IDENTIFIED BY` : définit un mot de passe

---

### 🛠️ b. Accorder des privilèges avec `GRANT`
```sql
GRANT SELECT ON base_de_donnees.* TO 'analyste1'@'localhost';
```

- `SELECT` : le droit accordé
- `ON base_de_donnees.*` : toute la base, ou une table spécifique
- `TO` : cible de la permission

---

### ❌ c. Révoquer un privilège avec `REVOKE`
```sql
REVOKE SELECT ON base_de_donnees.* FROM 'analyste1'@'localhost';
```

> Supprime le droit `SELECT` sur toute la base `base_de_donnees`.

---

### 🧪 d. Vérifier les privilèges
```sql
SHOW GRANTS FOR 'analyste1'@'localhost';
```

> Affiche tous les droits actuellement attribués à l’utilisateur.

---

## 📊 4. Cas d’usage : l’analyste lecture seule

**Contexte :**
Vous avez une base `sales_db`. Un utilisateur doit **lire** des données, mais ne doit pas **modifier, insérer ou supprimer** quoi que ce soit.

### ✅ Étapes :
```sql
-- Création de l'utilisateur
CREATE USER 'reader_ana'@'%' IDENTIFIED BY 'AnaLis@123';

-- Attribution du privilège SELECT uniquement
GRANT SELECT ON sales_db.* TO 'reader_ana'@'%';

-- Vérification
SHOW GRANTS FOR 'reader_ana'@'%';
```

---

## ✅ Bonnes pratiques

- Utiliser des **rôles métiers** (RBAC) pour standardiser les permissions
- Ne jamais utiliser l'utilisateur `root` pour les applications
- Toujours tester les droits attribués à un utilisateur avec `SHOW GRANTS`
- **Limiter la portée** (par table, schéma ou colonne si possible)
- Révoquer les droits obsolètes ou inutilisés

---

## 🎓 Exercices pratiques

### 🧩 Exercice 1
Créer un utilisateur `viewer1` qui peut lire uniquement la table `clients` dans la base `crm`.

> 🔁 Solution attendue :
```sql
CREATE USER 'viewer1'@'localhost' IDENTIFIED BY 'Secure123!';
GRANT SELECT ON crm.clients TO 'viewer1'@'localhost';
```

---

### 🧩 Exercice 2
Révoquer le droit d'insertion à l'utilisateur `editor2` sur toutes les tables de la base `project_db`.

> 🔁 Solution attendue :
```sql
REVOKE INSERT ON project_db.* FROM 'editor2'@'localhost';
```
