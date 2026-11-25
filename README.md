# 🛡️ Mon Portfolio — Analyste NOC N1 & Administration Système Linux

Bienvenue sur mon portfolio technique. Ce dépôt regroupe mes réalisations pratiques, mes laboratoires d'exploitation et mes procédures opérationnelles. Il illustre concrètement mes compétences en **Supervision d'Infrastructure (NOC)**, **Administration Système Linux** et **Cybersécurité Défensive**, dans le cadre de ma candidature au poste d'**Analyste NOC Niveau 1**.

---

## 🎯 Mon Positionnement Professionnel : Analyste NOC N1

Passionné par la fiabilité des infrastructures et la réactivité opérationnelle, je me positionne en première ligne pour garantir la haute disponibilité, détecter proactivement les anomalies et traiter méthodiquement les incidents. Mon profil s'articule autour de trois axes majeurs :

- **📊 Supervision Opérationnelle (NOC N1) :** Je maîtrise l'exploitation quotidienne de solutions de supervision industrielle comme **Zabbix 7.0 LTS**. Je sais concevoir des tableaux de bord orientés pilotage, interpréter en temps réel les télémétries système, acquitter et qualifier immédiatement les alertes pour respecter scrupuleusement les engagements de service (SLA / MTTA).
- **🐧 Administration Système Linux & Réseau :** J'administre des serveurs Linux (Debian, Ubuntu Server) en ligne de commande (CLI). Je maîtrise la gestion des démons via `systemd`, l'administration distante sécurisée via OpenSSH, la manipulation des sockets réseau (`ss`, `nc`) et le diagnostic de connectivité TCP/IP (`ping`, `traceroute`).
- **🔒 Rigueur d'Exploitation & Hygiène de Cybersécurité :** J'applique les bonnes pratiques défensives : administration sous principe de moindre privilège, analyse rigoureuse des journaux d'événements (`journalctl`, `auth.log`), contrôle de l'intégrité logicielle et formalisation sans faille de chaque intervention (traçabilité ITSM).

---

## 📋 Mes Projets & Labs Pratiques

| Projet / Lab | Domaine d'Application | Stack Technique | Mon Statut | Lien vers mon Travail |
| :--- | :--- | :--- | :---: | :--- |
| **Supervision d'Infrastructure & Gestion d'Incidents NOC** | Supervision NOC N1, Administration Linux, Résolution d'Incidents | Ubuntu 22.04 LTS, Zabbix 7.0 LTS, SSH, Linux CLI, Bash | ✅ Réalisé & Documenté | [Consulter mon Lab Zabbix](./zabbix-noc-monitoring/README.md) |
| **Mon Playbook d'Incident NOC Niveau 1** | Procédure Réflexe, Diagnostic CLI, Modèle d'Escalade N2 | Markdown, Linux CLI (`ps`, `top`, `ss`, `nc`, `journalctl`), ITSM | ✅ Réalisé & Documenté | [Consulter ma Fiche Réflexe](./zabbix-noc-monitoring/docs/noc-incident-playbook.md) |
| **Durcissement Serveur & Sécurisation SSH (Roadmap)** | Sécurité Défensive, Hardening Linux | Ubuntu 22.04 LTS, OpenSSH, UFW, Fail2ban | 🔄 En cours de labo | *Prochainement disponible* |
| **Centralisation & Analyse de Logs SIEM (Roadmap)** | Détection d'Intrusions, SOC / SIEM | Wazuh, Syslog, ELK | ⏳ En préparation | *Prochainement disponible* |

---

## ⚙️ Ma Démarche Opérationnelle d'Analyste NOC N1

Au sein d'un centre d'opérations réseau (NOC), je ne me contente pas de constater une alerte : j'applique une démarche rigoureuse et standardisée pour chaque événement :

### 1. Ma Réactivité et le Respect du MTTA (Mean Time to Acknowledge)
Dès qu'un trigger passe à l'état critique, **j'acquitte immédiatement l'alerte** dans la console de supervision en consignant un commentaire professionnel horodaté. Cela informe instantanément l'équipe qu'un analyste est en cours d'investigation et supprime tout risque de double prise en charge.

### 2. Ma Méthode de Diagnostic en 5 Étapes
Face à une dérive de métrique ou une alerte de service, je déroule systématiquement ma séquence réflexe :
1. **Acquittement & Qualification** de la criticité de l'alerte.
2. **Contrôle de joignabilité réseau** (couche 3 : ICMP, routes).
3. **Contrôle des ports et services d'écoute** (couche 4 : sockets `10050`, `22`, etc.).
4. **Diagnostic approfondi en SSH** (consommation CPU/RAM, disques, logs système et applicatifs).
5. **Résolution documentée ou escalade N2 argumentée** avec transmission de toutes les preuves collectées.

### 3. Ma Rigueur de Traçabilité
Pour moi, un incident n'est clos que lorsque son historique est fidèlement consigné. Que ce soit dans l'outil de billetterie (ITSM) ou dans Zabbix, je documente chaque commande exécutée, les valeurs observées et la justification technique du rétablissement.

---

## 📂 Organisation de Mon Répertoire

```text
cybersecurity-portfolio/
├── README.md                                  # Ma présentation, mon positionnement Analyste NOC N1 et ma démarche
└── zabbix-noc-monitoring/                     # Mon projet complet de supervision Zabbix 7.0 LTS
    ├── README.md                              # Mon rapport technique illustré (Phases 1 à 4)
    ├── docs/
    │   └── noc-incident-playbook.md           # Ma fiche réflexe 5 étapes & mon modèle de ticket d'escalade N2
    └── screenshots/                           # Mes 8 captures d'écran prouvant la réalisation du lab
        ├── installation-server-ssh.png
        ├── phase2-01-agent-install.png
        ├── phase2-01-agent-status.png
        ├── phase2-02-host-active.png
        ├── phase3-01-dashboard-empty.png
        ├── phase3-02-dashboard-completed.png
        ├── phase4-01-alert-acknowledged.png
        └── phase4-02-alert-resolved.png
```

---

## 📬 Me Contacter & Échanger

- **Poste visé :** Analyste NOC Niveau 1 / Technicien d'Exploitation & Supervision / Administrateur Système Junior
- **Profil technique :** Linux (Ubuntu/Debian), Zabbix 7.0, OpenSSH, Réseaux TCP/IP, Fiches Réflexes & ITSM
- **Dépôt GitHub :** [cybersecurity-portfolio](https://github.com/)

---
*Ce portfolio technique démontre ma capacité immédiate à intégrer une équipe NOC et à gérer des incidents d'infrastructure selon les meilleures pratiques du secteur.*
