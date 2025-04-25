# 📊 Chapitre 7 : Surveillance et Audit des Bases de Données

---

## 🎯 Objectifs pédagogiques

À l’issue de ce chapitre, vous serez capable de :
- Mettre en place la **journalisation des accès** à une base de données
- Activer et interpréter les **logs systèmes** et SQL
- Implémenter un **audit trail** pour la conformité RGPD/ISO
- Analyser les logs pour détecter des comportements suspects
- Intégrer la base de données à une solution de **SIEM**

---

## 🧐 1. Pourquoi auditer une base de données ?

### 📌 Objectifs :
- **Suivre les accès et activités utilisateurs**
- Détecter les comportements **anormaux ou malveillants**
- **Assurer la traçabilité** : qui a fait quoi, quand, comment ?
- Répondre aux exigences **légales et normatives** (RGPD, ISO 27001, PCI-DSS…)

---

## 🧩 2. Types de logs utiles

### 📁 a. Logs du moteur SQL (MySQL, PostgreSQL, etc.)

| Type de log          | Contenu                                              |
|----------------------|------------------------------------------------------|
| `general_log`        | Toutes les requêtes exécutées                        |
| `slow_query_log`     | Requêtes lentes (selon un seuil)                     |
| `error_log`          | Échecs de connexions, crashs, erreurs systèmes       |
| `audit_log`          | Accès, création, suppression, modifications (si activé) ✅ |

---

### 📁 b. Logs système
- Connexions SSH : `/var/log/auth.log` (Linux)
- Erreurs kernel : `/var/log/syslog`, `/var/log/messages`
- Règles de sécurité : `/var/log/fail2ban.log`, `/var/log/firewall.log`

---

## 🧪 3. Activer les logs utiles

### 🧰 a. MySQL (fichier `my.cnf`)
```ini
[mysqld]
general_log = 1
general_log_file = /var/log/mysql/mysql.log

slow_query_log = 1
long_query_time = 2
log_error = /var/log/mysql/error.log
```

Redémarrer :
```bash
sudo systemctl restart mysql
```

---

### 🧰 b. PostgreSQL (`postgresql.conf`)
```conf
logging_collector = on
log_directory = 'log'
log_filename = 'postgresql.log'
log_statement = 'all'
log_duration = on
log_connections = on
log_disconnections = on
```

---

## 📎 4. Plugin d’audit MySQL

MySQL Enterprise ou MariaDB proposent un **audit plugin**.

### 📦 MariaDB – Activation :
```sql
INSTALL SONAME 'server_audit';
SET GLOBAL server_audit_logging = ON;
```

> 📄 Journalise les événements dans `/var/lib/mysql/server_audit.log`

---

## 🔍 5. Analyse des logs

### 🧠 Ce qu’il faut surveiller :
- Tentatives de connexion échouées (surtout par des IP inconnues)
- Exécutions de requêtes anormalement longues ou rares
- Accès aux tables sensibles (ex : `users`, `payments`)
- Changement de mot de passe ou suppression de compte

---

### 📊 Intégration dans un SIEM (Splunk, ELK, Wazuh…)

#### Bénéfices :
- Centralisation de logs de plusieurs serveurs
- Tableaux de bord personnalisés
- Alertes automatiques (mail, Slack, webhook)
- Archivage et rotation

#### Exemple de pipeline :
MySQL → Filebeat → Logstash → Elasticsearch → Kibana

---

## 🔒 6. Conformité RGPD / ISO / PCI-DSS

| Norme / Règlement     | Exigences principales liées à l’audit |
|-----------------------|----------------------------------------|
| **RGPD (UE)**         | Journalisation des accès aux données personnelles ✅ |
| **ISO/IEC 27001**     | Traces d’audit sur les opérations critiques ✅ |
| **PCI-DSS**           | Logs sur 12 mois, horodatage sécurisé ✅ |

---

## ✅ Bonnes pratiques

- **Activer les logs** en environnement de production (ne pas les désactiver pour les performances !)
- **Stocker les logs sur un disque séparé** ou dans un serveur de log distant
- Restreindre l’accès aux fichiers logs (`chmod 600`)
- Mettre en place une **politique de rotation et d’archivage** (`logrotate`)
- Définir une **durée de conservation** : ex. 1 an pour les logs critiques
- Ajouter un **horodatage précis et sécurisé** (NTP activé)

---

## 🧪 Exercices pratiques

### 🧩 Exercice 1
Activer le `general_log` et le `slow_query_log` pour MySQL avec un seuil à 1 seconde.

> ✅ Dans `my.cnf` :
```ini
[mysqld]
general_log = 1
slow_query_log = 1
long_query_time = 1
```

---

### 🧩 Exercice 2
Analyser ce log :
```
[Warning] Access denied for user 'admin'@'192.168.1.88'
```
→ Quelle est l’origine ? Que faire ?

> ✅ Réponse attendue :
Tentative de connexion échouée. Ajouter IP à Fail2ban, vérifier si récurrente, analyser le niveau de privilège tenté.

---

### 🧩 Exercice 3
Configurer PostgreSQL pour logger toutes les connexions et durées de requêtes.

> ✅ `postgresql.conf` :
```conf
log_connections = on
log_disconnections = on
log_duration = on
```
