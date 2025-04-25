# 📚 Chapitre 9 : Normes et Conformité

---

## 🎯 Objectifs pédagogiques

À l’issue de ce chapitre, vous serez capable de :
- Identifier les principales normes de sécurité applicables aux bases de données
- Comprendre les exigences du **RGPD**, de **l’ISO/IEC 27001**, et d'autres standards
- Mettre en œuvre des pratiques de conformité dans la gestion des données
- Rédiger des politiques et procédures de sécurité conformes

---

## 🏛️ 1. Pourquoi la conformité est-elle cruciale ?

### 📌 Raisons :
- **Obligations légales** : RGPD en Europe, HIPAA aux USA, etc.
- **Protection de la réputation** : fuite de données = perte de confiance
- **Éviter les sanctions** financières (jusqu’à 4 % du CA mondial selon RGPD)
- Structuration des **politiques de sécurité**

---

## ⚖️ 2. Principales normes et réglementations

### 📘 a. RGPD (Règlement Général sur la Protection des Données)

| Exigence                          | Application à la base de données                       |
|----------------------------------|--------------------------------------------------------|
| Minimisation des données         | Ne stocker que les données strictement nécessaires ✅   |
| Droits d’accès/suppression       | Permettre l’effacement des données utilisateur ✅       |
| Journalisation des accès         | Logs des actions et connexions à la base ✅             |
| Notification des violations      | Déclarer toute fuite de données sous 72h ✅             |
| Chiffrement ou pseudonymisation  | Appliquer aux données sensibles ✅                      |

> 🔎 Articles clés : 5, 25, 32, 33, 34 du RGPD

---

### 📘 b. ISO/IEC 27001 : Système de management de la sécurité de l’information

| Domaine                          | Application à la base                                  |
|----------------------------------|--------------------------------------------------------|
| Politique de sécurité            | Documentée, approuvée, diffusée                        |
| Contrôle d’accès (A.9)           | Authentification forte, rôles, principe du moindre privilège ✅ |
| Cryptographie (A.10)             | Données en transit et au repos chiffrées ✅             |
| Sauvegardes (A.12.3)             | Planifiées, testées, documentées ✅                     |
| Journalisation et surveillance   | Logs activés, revus régulièrement ✅                    |

---

### 📘 c. PCI-DSS (pour les données de cartes bancaires)

| Exigence                          | Recommandation                                          |
|----------------------------------|----------------------------------------------------------|
| Chiffrement des données          | Chiffrement AES-256 au repos et en transit ✅             |
| Accès limité aux données         | ACL, rôle "lecture seule" pour les agents ✅              |
| Rotation des logs                | 12 mois de conservation minimum ✅                        |
| Tests réguliers de vulnérabilité | Scan de sécurité + pentests semestriels ✅               |

---

## 🧾 3. Bonnes pratiques pour assurer la conformité

### ✅ a. Documentation
- Politique de sécurité des bases de données
- Registre des traitements (pour RGPD)
- Plan de sauvegarde et PRA
- Traçabilité des accès et modifications

---

### ✅ b. Journalisation & traçabilité
- Activer les logs : connexions, erreurs, actions sur données critiques
- Intégration à une solution SIEM (Wazuh, ELK, Splunk)

---

### ✅ c. Gestion des accès
- Authentification forte (2FA ou clés SSH)
- Rôles et profils restreints (RBAC)
- Révocation immédiate des anciens comptes

---

### ✅ d. Revue de sécurité régulière
- Audit technique interne ou externe
- Vérification de la conformité avec le RGPD/ISO
- Tests d'intrusion annuels

---

## 🧠 4. Cas concret de conformité RGPD sur une base MySQL

### Contexte :
Une application stocke les données de clients (nom, e-mail, historique d’achat)

#### ✔️ À mettre en place :
- Chiffrement AES sur les emails
- Journalisation des accès à la table `clients`
- Formulaire pour demander l’effacement des données
- Logique d’anonymisation sur l’historique après X années

---

## ✅ Bonnes pratiques

- Documenter les finalités de chaque table de données
- Ne jamais utiliser de données réelles en environnement de test
- Mettre en place une **anonymisation ou pseudonymisation** des données sensibles
- Préparer un **registre des incidents** et un protocole de notification
- Sensibiliser les équipes aux obligations réglementaires

---

## 🧪 Exercices pratiques

### 🧩 Exercice 1
Lister les données personnelles présentes dans une base `users` et proposer pour chacune une mesure de conformité RGPD.

> Exemple : `email` → chiffrement, `ip_address` → anonymisation ou consentement.

---

### 🧩 Exercice 2
Proposer une politique de journalisation conforme à l’ISO 27001 pour un serveur PostgreSQL.

> ✅ Inclure : `log_statement = all`, durée de conservation, rotation des logs.

---

### 🧩 Exercice 3
Créer une procédure de suppression des données utilisateur à la demande (article 17 RGPD).

> ✅ Script SQL + description d’un processus métier d’effacement.
