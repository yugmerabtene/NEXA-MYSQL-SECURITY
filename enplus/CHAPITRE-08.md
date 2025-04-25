# 💾 Chapitre 8 : Sauvegardes et Reprise après Incident

---

## 🎯 Objectifs pédagogiques

À l’issue de ce chapitre, vous serez capable de :
- Mettre en œuvre une stratégie de sauvegarde adaptée à une base de données
- Comprendre les types de sauvegardes (complète, incrémentale, logique, physique)
- Automatiser les sauvegardes avec `cron` ou des outils spécialisés
- Effectuer une **restauration fiable**
- Préparer un **plan de reprise après incident (PRA)**

---

## 🚨 1. Pourquoi sauvegarder ?

### 📌 Raisons principales :
- Prévenir la **perte de données** (panne disque, erreur humaine, suppression accidentelle)
- Répondre aux exigences de **conformité** (RGPD, ISO 27001)
- Permettre une **restauration rapide** en cas d’incident
- Se protéger des **ransomwares ou attaques destructives**

---

## 🧱 2. Types de sauvegardes

| Type                     | Description                                         | Exemple outil     |
|--------------------------|-----------------------------------------------------|-------------------|
| Sauvegarde **complète**  | Copie intégrale de la base                          | `mysqldump`, `pg_dump` |
| Sauvegarde **incrémentale** | Copie des données modifiées depuis la dernière sauvegarde | `xtrabackup`, `pgBackRest` |
| Sauvegarde **logique**   | Export SQL (textuel) des données                   | `mysqldump`        |
| Sauvegarde **physique**  | Copie des fichiers binaires de la base             | `rsync`, snapshots |

---

## 🛠️ 3. Outils de sauvegarde

### 🔧 a. Pour MySQL/MariaDB

#### ✅ `mysqldump` – export logique
```bash
mysqldump -u root -p nom_base > sauvegarde.sql
```

#### ✅ `xtrabackup` – sauvegarde à chaud (physique)
```bash
xtrabackup --backup --target-dir=/sauvegardes/xtrabackup
```

---

### 🔧 b. Pour PostgreSQL

#### ✅ `pg_dump` – export logique
```bash
pg_dump nom_base > sauvegarde_pg.sql
```

#### ✅ `pg_basebackup` – sauvegarde physique
```bash
pg_basebackup -D /sauvegardes/pg -Fp -Xs -P
```

---

### 🧮 c. Automatisation avec `cron`
#### Exemple : sauvegarde quotidienne à 3h du matin
```bash
0 3 * * * /usr/bin/mysqldump -u root -pMonMotDePasse base1 > /backups/base1_$(date +\%F).sql
```

> ✅ Penser à **chiffrer les sauvegardes** et **les déplacer hors du serveur principal**

---

## 🔐 4. Sécuriser les sauvegardes

- Chiffrement avec `gpg` ou `openssl`
```bash
openssl enc -aes-256-cbc -salt -in backup.sql -out backup.sql.enc
```

- Stockage distant : NAS, SFTP, Amazon S3, Azure Blob, etc.
- Contrôle des accès : pas d’accès root aux répertoires de sauvegarde
- Hash de vérification d’intégrité (ex : `sha256sum`)

---

## ♻️ 5. Reprise après incident (PRA)

### 🧨 Scénarios à anticiper :
- Suppression accidentelle
- Corruption de données
- Attaque (ransomware, sabotage)
- Panne système/disque

---

### 🛠️ Étapes d’un PRA efficace :

1. **Détecter** et qualifier l’incident
2. **Isoler** le système affecté (déconnexion réseau si nécessaire)
3. **Évaluer** les pertes de données
4. **Restaurer** la base depuis la dernière sauvegarde fiable
5. **Vérifier** l’intégrité des données restaurées
6. **Documenter** l’incident

---

## 🔁 6. Restauration

### 🔄 Exemple avec `mysqldump` :
```bash
mysql -u root -p nom_base < sauvegarde.sql
```

### 🔄 Exemple avec `pg_dump` :
```bash
psql -U postgres -d nouvelle_base < sauvegarde_pg.sql
```

> ⚠️ Avant toute restauration : **sauvegarder l’état actuel**, même corrompu.

---

## ✅ Bonnes pratiques

- Sauvegarde **quotidienne** pour les bases actives
- Garder **7 jours de rétention** minimum
- Tester régulièrement les **restaurations**
- Chiffrer et externaliser les sauvegardes
- Documenter et automatiser le processus de PRA
- Utiliser des **noms de fichiers horodatés** (`backup_2025-04-25.sql`)

---

## 🧪 Exercices pratiques

### 🧩 Exercice 1
Créer une sauvegarde de la base `crm` chaque nuit à 2h, dans `/backups/crm/`.

> ✅ Fichier crontab :
```bash
0 2 * * * /usr/bin/mysqldump -u root -pMonMotDePasse crm > /backups/crm/crm_$(date +\%F).sql
```

---

### 🧩 Exercice 2
Restaurer la base `sales_db` à partir du fichier `sales_db_2025-04-01.sql`.

> ✅ Commande :
```bash
mysql -u root -p sales_db < /backups/sales_db_2025-04-01.sql
```

---

### 🧩 Exercice 3
Chiffrer une sauvegarde avec `openssl` et générer son hash.

> ✅ Commandes :
```bash
openssl enc -aes-256-cbc -in backup.sql -out backup.sql.enc
sha256sum backup.sql.enc > backup.sha256
```
