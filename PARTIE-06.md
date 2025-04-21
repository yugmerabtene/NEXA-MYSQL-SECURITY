# 📊 **PARTIE 6 – Monitoring et audit de sécurité**

---

## 🎯 Objectif global :
Mettre en place des **outils de supervision** pour :
- détecter les **tentatives d'intrusion**
- analyser les **logs systèmes et applicatifs**
- être **alerté en cas d’anomalie**
- avoir une **vue en temps réel** de la santé du serveur

---

## 🧩 ÉTAPE 1 – Analyse automatique des logs : `logwatch`

---

### 📦 Installation :
```bash
sudo apt install logwatch -y
```

### ⚙️ Configuration de base :
```bash
sudo nano /usr/share/logwatch/default.conf/logwatch.conf
```

Vérifie ou modifie :
```ini
MailTo = root
Detail = Med
Range = yesterday
```

> 🔔 Les rapports seront envoyés quotidiennement à `root` (redirigeable par alias mail)

---

## 📈 ÉTAPE 2 – Analyse en temps réel des accès web : `goaccess`

---

### 📦 Installation :
```bash
sudo apt install goaccess -y
```

### 📊 Utilisation manuelle :
```bash
sudo goaccess /var/log/apache2/access.log -o /var/www/html/report.html --log-format=COMBINED
```

➡️ Tu peux consulter le rapport sur `http://IP/report.html`

---

## 🧠 ÉTAPE 3 – Vérifications de sécurité système : `rkhunter` et `chkrootkit`

---

### 📦 Installation :
```bash
sudo apt install rkhunter chkrootkit -y
```

### 🔍 Analyse ponctuelle :
```bash
sudo chkrootkit
sudo rkhunter --update
sudo rkhunter --check
```

➡️ Recherche de rootkits, fichiers suspects, comportements anormaux.

---

## 🕵️ ÉTAPE 4 – Scanner de sécurité Linux : `lynis`

---

### 📦 Installation :
```bash
sudo apt install lynis -y
```

### 🛡️ Analyse complète du système :
```bash
sudo lynis audit system
```

➡️ Il fournit une **note de sécurité**, des recommandations (pare-feu, droits, kernel, etc.)

---

## 📦 ÉTAPE 5 – Monitoring avancé (optionnel)

---

### 🔍 Objectif :
Avoir un **tableau de bord complet** avec alertes (CPU, RAM, disques, accès, logs...)

#### 🟢 Solutions possibles :
- **Zabbix** (infrastructure complète auto-hébergée)
- **Grafana + Prometheus** (metrics visuels)
- **Netdata** (facile, visuel, en temps réel)

---

### 💡 Installation de Netdata (ultra simple, conseillé) :
```bash
bash <(curl -Ss https://my-netdata.io/kickstart.sh)
```

➡️ Dashboard sur : `http://IP:19999`

---

## 📂 ÉTAPE 6 – Audit régulier des permissions et fichiers sensibles

### Script de contrôle des fichiers critiques :
```bash
#!/bin/bash
echo "Vérification des fichiers sensibles :"

find /var/www/html -type f -name "*.php" -exec stat -c "%a %n" {} \;
stat -c "%a %n" /var/www/wp-config.php
stat -c "%a %n" /etc/apache2/apache2.conf
stat -c "%a %n" /etc/mysql/my.cnf
```

Tu peux automatiser ce script avec `cron` pour avoir un rapport hebdomadaire.

---

## ✅ Résumé des outils de surveillance & audit

| Outil/Commande          | Fonction                                 |
|-------------------------|------------------------------------------|
| `logwatch`              | Rapport quotidien des logs système       |
| `goaccess`              | Statistiques accès web interactives      |
| `chkrootkit`, `rkhunter`| Détection de rootkits et comportements   |
| `lynis`                 | Audit de sécurité complet                |
| `Netdata`               | Monitoring temps réel (CPU, RAM, etc.)  |
| `cron + script`         | Contrôle automatique des permissions     |

---

## 🔁 BONUS – Automatiser les audits avec `cron`

Exemple : audit sécurité hebdomadaire
```bash
crontab -e
```

Ajouter :
```cron
0 2 * * 1 /usr/bin/lynis audit system > /var/log/lynis_report.log
```
