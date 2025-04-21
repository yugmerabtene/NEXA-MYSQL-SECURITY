# 📘 **Sommaire du Cours Complet : Sécurisation d’un Serveur Linux avec MySQL, phpMyAdmin et WordPress**

---

## 🔐 PARTIE 1 – SÉCURISATION DU SERVEUR LINUX

1. **Mise à jour du système et création d’un utilisateur non-root**
2. **Accès SSH sécurisé via clé (PuTTY/PuTTYgen)**
3. **Configuration avancée de SSH (`sshd_config`)**
4. **Pare-feu UFW : politique stricte et ports minimaux**
5. **Fail2ban : protection contre les attaques bruteforce**
6. **Suricata (IDS) ou alternative : détection d’intrusion**
7. WAF Web Application Firewall
8. ✅ **💡 Améliorations proposées**
   - Changement automatique de port SSH à intervalle régulier
   - Notification d’alerte par mail (Fail2ban + logwatch)
   - Mise en place de journaux centralisés (rsyslog / Graylog / ELK)

---

## 🧱 PARTIE 2 – INSTALLATION SÉCURISÉE DU STACK LAMP

1. **Installation d’Apache, PHP, MySQL (mariadb optional)**
2. **Sécurisation de MySQL (`mysql_secure_installation`)**
3. **Création de comptes MySQL restreints par base**
4. ✅ **💡 Améliorations proposées**
   - Séparation logique des privilèges : lecture seule, écriture, admin
   - Activation de `mysql_audit_plugin`
   - Sauvegarde automatique chiffrée avec `mysqldump + GPG`

---

## 🔐 PARTIE 3 – SÉCURISATION DES SERVICES WEB

1. **Installation de phpMyAdmin avec protection par alias**
2. **Redirection automatique HTTP ➜ HTTPS**
3. **Configuration SSL auto-signée ou Let’s Encrypt**
4. **Activation des `.htaccess` et protection par mot de passe**
5. ✅ **💡 Améliorations proposées**
   - Mise en place d’un proxy inverse NGINX avec Apache en backend
   - Activation des headers HTTP sécurisés (Content-Security-Policy, X-Frame-Options…)
   - Sécurisation avec ModSecurity + règles OWASP

---

## 🌐 PARTIE 4 – INSTALLATION ET CONFIGURATION DE WORDPRESS

1. **Téléchargement, extraction et permissions**
2. **Connexion à la base de données**
3. **Sécurisation WordPress (wp-config.php, droits, clés uniques)**
4. ✅ **💡 Améliorations proposées**
   - Déplacement de `wp-config.php` à un niveau supérieur
   - Installation du plugin "Wordfence" ou "iThemes Security"
   - Changer le préfixe `wp_` des tables
   - Désactivation de l’éditeur de fichier dans le back-office
   - Protection anti-DDoS avec Cloudflare gratuit

---

## 🔑 PARTIE 5 – GESTION DES MOTS DE PASSE ET DES SAUVEGARDES

1. **Utilisation d’un gestionnaire de mots de passe (KeePassXC)**
2. **Stockage local chiffré des secrets (GPG, chiffrage de fichiers)**
3. **Sauvegarde automatique du site et de la base de données**
4. ✅ **💡 Améliorations proposées**
   - Utilisation de Vault (HashiCorp) ou Pass (Linux)
   - Rotation des mots de passe administrateurs
   - Planification avec `cron` + logs de backup

---

## 📊 PARTIE 6 – MONITORING ET AUDIT DE SÉCURITÉ

1. **Installation de `logwatch` ou `goaccess` pour logs Apache**
2. **Analyse automatique des logs MySQL, SSH, WordPress**
3. **Script de vérification des permissions critiques**
4. ✅ **💡 Améliorations proposées**
   - Intégration d’un dashboard (Zabbix / Grafana + Prometheus)
   - Scan automatique de vulnérabilités (Lynis, ClamAV, OpenVAS)
   - Audit mensuel de configuration (chkrootkit, rkhunter)

---

## 🔁 BONUS – AUTOMATISATION ET CI/CD DE DÉPLOIEMENT

1. **Script Bash pour répliquer l’installation**
2. **Utilisation d’Ansible pour l’automatisation**
3. **CI/CD de déploiement WordPress (Git + rsync ou GitHub Actions)**
4. **Audit avec SCAP / OpenSCAP**
