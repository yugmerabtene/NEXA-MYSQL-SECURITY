# 🧠 Chapitre 1 : Introduction à la Sécurité des Bases de Données

---

## 🎯 Objectifs pédagogiques

À l’issue de ce chapitre, vous serez capable de :
- Comprendre les **enjeux de sécurité** des bases de données
- Identifier les **menaces courantes** pesant sur les SGBD
- Distinguer les notions de **confidentialité, intégrité, disponibilité**
- Connaître les **types d’attaques** les plus fréquents

---

## 🏛️ 1. Pourquoi sécuriser une base de données ?

### 📌 Rôle central des bases de données :
- Stockage d’informations critiques : clients, salariés, paiements, santé…
- Cible privilégiée des **cyberattaques**
- Enjeu juridique (RGPD, loi Informatique & Libertés)

### 🎯 Objectifs de la sécurité des données :
On parle souvent du **triptyque CIA** :
| Élément         | Objectif                                            |
|-----------------|-----------------------------------------------------|
| **Confidentialité** | Empêcher l’accès non autorisé aux données         |
| **Intégrité**        | Empêcher la modification frauduleuse ou accidentelle |
| **Disponibilité**    | Garantir l’accès aux données quand nécessaire     |

---

## 🚨 2. Menaces pesant sur les bases de données

### ⚠️ a. Menaces internes
- Employés malveillants ou négligents
- Mauvaises configurations
- Accès excessifs ou non justifiés

### ⚠️ b. Menaces externes
- Hackers, ransomware, botnets
- Exploitation de vulnérabilités (ex : injection SQL)
- Vol de sauvegardes ou attaque réseau

---

## 🎯 3. Types d’attaques fréquentes

### 💉 a. Injection SQL
Manipulation d’une requête SQL via des entrées utilisateur mal sécurisées.

**Exemple :**
```sql
SELECT * FROM users WHERE email = '$email' AND password = '$password';
```
Si `$email = admin' --`, alors la requête devient :
```sql
SELECT * FROM users WHERE email = 'admin' --' AND password = '';
```
→ Authentification contournée

---

### 🔐 b. Brute Force
Essai répété de mots de passe pour accéder à un compte (ex. root, admin).

### 🌍 c. Exploitation de failles réseau
Port 3306 ouvert à tous, accès root sans mot de passe, services exposés à Internet.

### 🧨 d. Ransomware ciblant les bases
Cryptage de fichiers `.ibd`, `.frm`, `.sql`, puis demande de rançon.

---

## 🧱 4. Surfaces d’attaque potentielles

| Surface              | Exemples                                                      |
|----------------------|---------------------------------------------------------------|
| Interfaces web       | Formulaires non filtrés                                       |
| Réseau               | Port ouvert, SSH, accès à distance non protégé                |
| Comptes utilisateurs | Utilisateur root exposé, pas de séparation des privilèges     |
| Système              | Logs en lecture libre, fichiers `.sql` accessibles            |
| Applications tierces | CMS mal configuré (ex : WordPress avec plugin vulnérable)     |

---

## 📐 5. Architecture classique d’un système vulnérable

> 🔎 Exemple d'architecture risquée :
- Serveur web et base de données sur le même hôte
- Port MySQL ouvert sur Internet
- Utilisateur `root` utilisé par l’application
- Pas de chiffrement des données ni des sauvegardes
- Aucun contrôle d’accès réseau

---

## 🛡️ 6. Premiers réflexes à adopter

### ✅ Checklist de base :
- Désactiver l’accès distant au compte `root`
- Fermer les ports inutiles
- Appliquer le **principe du moindre privilège**
- Sauvegarder et **chiffrer** les dumps SQL
- Activer les logs (accès, erreurs, requêtes lentes)
- Mettre à jour le SGBD et les dépendances
- Utiliser des **requêtes préparées** (contre SQLi)

---

## 📘 7. Sécurité dès la conception : "Security by Design"

Dès la **phase de modélisation**, il faut :
- Anticiper les types de données sensibles
- Catégoriser les droits utilisateurs
- Prévoir l’anonymisation, le chiffrement, l'audit
- Intégrer les exigences RGPD et normes ISO

---

## ✅ Bonnes pratiques

- Ne jamais laisser de mots de passe en clair
- Éviter les utilisateurs administrateurs pour l’application
- Contrôler les exports SQL (fichier `.sql` ou `.csv`)
- Faire auditer la configuration de la base et du système
- Appliquer des politiques de rotation des clés et mots de passe

---

## 🧪 Exercices pratiques

### 🧩 Exercice 1
Lister les risques pour une base MySQL exposée en clair sur le port 3306, avec un utilisateur root sans mot de passe.

> ✅ Attendu : accès non contrôlé, modification ou suppression de données, attaque via scripts automatisés.

---

### 🧩 Exercice 2
Sur une base contenant les colonnes suivantes : `email`, `password`, `num_carte`, `adresse`, que recommanderiez-vous pour sécuriser chaque champ ?

> ✅ Réponses attendues :
- `email` : validation + chiffrement
- `password` : hachage (bcrypt, Argon2)
- `num_carte` : chiffrement fort AES-256
- `adresse` : anonymisation selon usage

---
