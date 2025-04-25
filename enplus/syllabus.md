### 📘 **Chapitre 1 : Introduction à la sécurité des bases de données**

**Objectifs :**
- Comprendre les enjeux de sécurité
- Identifier les menaces potentielles
- Connaître les types d'attaques les plus courants

**Contenu :**
- L’importance des données (confidentialité, intégrité, disponibilité)
- Principales menaces : accès non autorisé, perte de données, altération des données
- Types d'attaques : SQLi, brute force, DoS, accès non contrôlé
- Exemple : attaque SQLi simple sur un champ de login mal protégé

---

### 🔐 **Chapitre 2 : Le principe du moindre privilège**

**Objectifs :**
- Appliquer le modèle RBAC (Role-Based Access Control)
- Comprendre comment restreindre les accès utilisateurs

**Contenu :**
- Principe du moindre privilège : donner uniquement les droits nécessaires
- Gestion des utilisateurs et rôles
- Commandes SQL utiles : `CREATE USER`, `GRANT`, `REVOKE`
- Exemple : accorder seulement `SELECT` à un utilisateur analyste

---

### 🧾 **Chapitre 3 : Configuration sécurisée du serveur de base de données**

**Objectifs :**
- Sécuriser la configuration système et réseau
- Protéger l’accès physique et logique au serveur

**Contenu :**
- Configuration du fichier `my.cnf` (bind-address, skip-name-resolve, disable symbolic-links, etc.)
- Restriction du port 3306 (firewall ou iptables)
- Désactivation de l’accès root distant
- Sécurisation des permissions fichiers sur Linux (`chmod`, `chown`)
- Segmentation réseau

---

### 🧰 **Chapitre 4 : Outils de défense et détection**

**Objectifs :**
- Utiliser des outils de sécurité pour surveiller et défendre la base

**Contenu :**
- Pare-feu (iptables, UFW)
- WAF (Web Application Firewall) : ModSecurity
- Fail2ban : détection et blocage des tentatives d'accès répétées
- Journalisation (logs MySQL, journaux système `/var/log/`)

---

### 🧪 **Chapitre 5 : Prévention des injections SQL**

**Objectifs :**
- Comprendre et empêcher les attaques SQLi

**Contenu :**
- Fonctionnement des injections SQL
- Requêtes préparées avec PDO (PHP) ou paramètres liés (Python, Java)
- Filtres et validations des entrées utilisateur (regex, whitelisting)
- Exemple : comment une mauvaise requête peut devenir vulnérable

---

### 🔐 **Chapitre 6 : Chiffrement et hachage des données**

**Objectifs :**
- Sécuriser les données stockées

**Contenu :**
- Chiffrement symétrique/asymétrique (AES, RSA)
- Chiffrement des colonnes sensibles
- Hachage des mots de passe (SHA-256, bcrypt, Argon2)
- Salage des hashs (protection contre rainbow tables)

---

### 🧬 **Chapitre 7 : Surveillance et audit**

**Objectifs :**
- Suivre les accès et les modifications sur la base

**Contenu :**
- Activation de logs d’audit (MySQL audit plugin, PostgreSQL pgaudit)
- Analyse de logs et alertes
- Intégrité des fichiers (hash, checksums)

---

### 🧱 **Chapitre 8 : Sauvegardes et reprise après incident**

**Objectifs :**
- Mettre en place une stratégie de backup/restauration

**Contenu :**
- Types de sauvegardes (complète, incrémentale, logique vs physique)
- Outils : `mysqldump`, `xtrabackup`, `pg_dump`
- Tests de restauration
- Chiffrement et stockage externe des backups

---

### 🧯 **Chapitre 9 : Normes et conformité**

**Objectifs :**
- Comprendre les exigences de conformité

**Contenu :**
- RGPD (anonymisation, durée de conservation, registre des traitements)
- ISO/IEC 27001 (sécurité de l'information)
- ISO/IEC 27037 (forensic), ISO/IEC 27701 (RGPD)
- Cas d’usage : entreprise traitant des données de santé ou fiscales

---

### 👩‍🔬 **Chapitre 10 : Tests de sécurité et bonnes pratiques**

**Objectifs :**
- Auditer la sécurité de la base
- Appliquer des correctifs

**Contenu :**
- Outils d’audit : sqlmap, Metasploit, Nmap
- Checklist de sécurité
- Mises à jour régulières (MySQL/PostgreSQL, OS, firewall)
- Utilisation d’ACL réseau, VPN, SSO, 2FA

---

## 📚 Annexes et Travaux Pratiques

- **TP 1 : Audit d’une base avec sqlmap sur une appli vulnérable locale**
- **TP 2 : Mise en place de requêtes préparées avec PDO**
- **TP 3 : Configuration de Fail2ban pour MySQL**
- **TP 4 : Création d’un utilisateur avec des droits limités et audit de ses activités**
- **TP 5 : Mise en place d’un plan de sauvegarde + test de restauration**
