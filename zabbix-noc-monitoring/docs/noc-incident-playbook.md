# 📘 Mon Playbook Opérationnel — Fiche Réflexe Incident NOC Niveau 1

- **Auteur / Rôle visé :** Analyste NOC Niveau 1
- **Document ID :** `NOC-SOP-001`
- **Version :** 1.0 (Labo Personnel & Référence d'Exploitation)
- **Hôte de référence :** `noc-zabbix-prod` (Ubuntu Server 22.04 LTS / Zabbix 7.0 LTS)
- **Objectif :** Formaliser ma méthode personnelle d'intervention face à une alerte d'infrastructure

---

## 🎯 Ma Démarche d'Analyste NOC N1

En tant qu'Analyste NOC Niveau 1, ma priorité absolue face à une alerte est de **réagir vite**, de **qualifier avec précision** l'anomalie et d'**éviter toute perte de temps ou d'information**. 

J'ai conçu cette fiche réflexe pour standardiser ma propre démarche d'investigation en 5 étapes chronologiques. Elle me permet de garantir un temps moyen d'acquittement (**MTTA**) inférieur à 5 minutes et d'assurer une traçabilité irréprochable de chaque geste technique.

---

## 🔄 Mon Logigramme d'Intervention en 5 Étapes

```text
[ Alerte Détectée dans Zabbix ]
              │
              ▼
┌────────────────────────────────────────────────────────┐
│  Étape 1 : Mon Acquittement Immédiat (MTTA < 5 min)    │ ──> Commentaire d'exploitation consigné
└────────────────────────────────────────────────────────┘
              │
              ▼
┌────────────────────────────────────────────────────────┐
│  Étape 2 : Mon Test de Joignabilité Réseau (Couche 3)  │ ──> ping, traceroute, détection pertes
└────────────────────────────────────────────────────────┘
              │
              ▼
┌────────────────────────────────────────────────────────┐
│  Étape 3 : Mon Contrôle des Ports & Sockets (Couche 4) │ ──> nc, ss -tulpn, état du daemon
└────────────────────────────────────────────────────────┘
              │
              ▼
┌────────────────────────────────────────────────────────┐
│  Étape 4 : Mon Diagnostic Approfondi en SSH & Logs     │ ──> ps, top, free, df, journalctl
└────────────────────────────────────────────────────────┘
              │
              ├──────────────────────────────────────────┐
              ▼ (Résolution dans mon périmètre N1)       ▼ (Dépassement périmètre / SLA N1)
┌──────────────────────────────────────────────┐ ┌──────────────────────────────────────────┐
│  Ma Résolution & Clôture                     │ │  Étape 5 : Mon Escalade N2 Documentée    │
│  - Métriques revenues sous les seuils        │ │  - Ticket ITSM complet et normé          │
│  - Passage automatique au statut RESOLVED    │ │  - Transmission des logs et constats N1  │
└──────────────────────────────────────────────┘ └──────────────────────────────────────────┘
```

---

## 🛠️ Ma Méthode d'Intervention Détaillée en 5 Étapes

### Étape 1 : Mon Acquittement Immédiat (Acknowledge)
*Mon objectif : Avertir l'équipe NOC que je prends l'incident en charge pour éviter les doublons.*

1. Dès l'apparition du problème sur mon tableau de bord **NOC-PROD-Overview**, j'ouvre la vue d'alerte.
2. Je clique sur le bouton **Acknowledge**.
3. Je consigne systématiquement un message d'exploitation normé :
   ```text
   Alerte prise en compte par l'Analyste NOC N1 à [HH:MM]. Début de mon diagnostic réseau et système sur l'hôte noc-zabbix-prod.
   ```
4. J'évalue la criticité de l'incident (`Warning`, `Average`, `High`, `Disaster`) pour calibrer mon délai d'action.

---

### Étape 2 : Mon Test de Joignabilité Réseau
*Mon objectif : Valider la couche 3 et éliminer l'hypothèse d'une coupure réseau ou d'un hôte éteint.*

Depuis mon poste d'exploitation ou la passerelle réseau, j'exécute :

```bash
# Je transmets 4 paquets ICMP pour vérifier la réponse et la latence
ping -c 4 127.0.0.1

# En cas de coupure ou d'anomalie de routage, j'analyse le cheminement
traceroute 127.0.0.1
# ou avec tracepath :
tracepath 127.0.0.1
```

- **Si 100% de perte de paquets :** L'hôte est potentiellement crashé ou le lien réseau est rompu. Je prépare immédiatement l'escalade vers l'équipe Réseau / Infra.
- **Si 0% de perte :** La communication réseau est validée, je passe immédiatement au test applicatif (Étape 3).

---

### Étape 3 : Mon Contrôle des Ports et des Services d'Écoute
*Mon objectif : Vérifier si le service en cause ou l'agent de supervision écoute correctement sur ses sockets.*

Je teste la disponibilité des ports réseau :

```bash
# 1. Je teste la socket de mon agent Zabbix (port 10050)
nc -zv 127.0.0.1 10050

# 2. Je teste le port OpenSSH pour m'assurer que je peux me connecter en CLI (port 22)
nc -zv 127.0.0.1 22

# 3. Sur la machine (via SSH), je contrôle l'état d'écoute des processus
ss -tulpn | grep -E ':(10050|22)'
```

- **Si le port 10050 est fermé :** Le service `zabbix-agent` s'est arrêté ou un pare-feu local bloque le flux.
- **Si le port 22 est ouvert :** J'initie ma session d'investigation SSH pour l'Étape 4.

---

### Étape 4 : Mon Diagnostic Approfondi en Ligne de Commande & Analyse de Logs
*Mon objectif : Identifier la cause racine de la dérive (CPU, mémoire, disque, processus).*

Je me connecte en SSH sur `noc-zabbix-prod` et j'exécute méthodiquement mes commandes d'investigation :

#### 1. Analyse de la Charge Processeur & Processus Actifs
```bash
# J'affiche les 10 processus les plus gourmands en CPU
ps aux --sort=-%cpu | head -n 11

# J'observe dynamiquement le Load Average et la consommation des threads
top -b -n 1 | head -n 25
```
*Mon réflexe d'analyste : Si le Load Average dépasse le nombre de cœurs CPU, il y a saturation de la file d'attente système.*

#### 2. Analyse de la Mémoire Vive & du Swap
```bash
# Je vérifie la RAM disponible et l'utilisation du Swap
free -h
```
*Mon réflexe d'analyste : Une consommation anormale de Swap indique un risque imminent d'OOM Killer (Out Of Memory).*

#### 3. Analyse de l'Espace Disque et des Inodes
```bash
# Je vérifie l'espace restant sur les partitions
df -h

# Je contrôle la saturation éventuelle des inodes
df -i
```
*Mon réflexe d'analyste : Une partition `/var` ou `/` pleine à plus de 90% bloque immédiatement l'écriture des logs et des bases de données.*

#### 4. Analyse des Journaux Système & Daemons
```bash
# Je contrôle le statut et les 50 dernières lignes de logs de l'agent Zabbix
sudo systemctl status zabbix-agent
sudo journalctl -u zabbix-agent -n 50 --no-pager

# Je consulte les logs propres au démon Zabbix
sudo tail -n 50 /var/log/zabbix/zabbix_agentd.log

# Je cherche les erreurs critiques récentes dans le journal système
sudo journalctl -p err..emerg -n 30 --no-pager
```

---

### Étape 5 : Ma Documentation ITSM & Mes Critères d'Escalade N2
*Mon objectif : Clôturer proprement si le problème est résolu ou transmettre un dossier complet à l'ingénierie N2.*

#### Mes critères de résolution autonome au Niveau 1 :
- La charge système ou la mémoire est redescendue sous les seuils d'alerte.
- Le trigger Zabbix a automatiquement basculé au statut **RESOLVED**.
- L'agent Zabbix répond sans erreur et le voyant `ZBX` est vert.
- Je saisis mon compte rendu de clôture dans l'outil de gestion des tickets.

#### Mes critères d'escalade obligatoire vers le Niveau 2 :
- Incident non résolu au bout de 15 minutes d'investigation N1.
- Processus applicatif métier critique bloqué nécessitant des droits d'ingénierie avancés.
- Suspicion d'anomalie de sécurité (modifications inexpliquées de fichiers systèmes, flux suspects).
- Panne matérielle ou corruption de système de fichiers.

---

## 📝 Mon Modèle de Ticket d'Escalade N2 (Basé sur Mon Incident Réel `noc-zabbix-prod`)

Voici le modèle de ticket d'escalade que j'applique pour transmettre un dossier au Niveau 2 sans perte d'information technique :

```text
================================================================================
                    TICKET D'INCIDENT NOC - ESCALADE NIVEAU 2
================================================================================
RÉFÉRENCE TICKET     : INC-20261125-0842
DATE & HEURE (UTC)   : 2026-11-25 15:40:00
ANALYSTE NOC N1      : Analyste NOC N1 (Mon Poste d'Exploitation)
CANAL DE DÉTECTION   : Alerte automatique Zabbix Server 7.0 LTS
CRITICITÉ / PRIORITÉ : ÉLEVÉE (P2)
--------------------------------------------------------------------------------
1. ÉLÉMENT DE CONFIGURATION IMPACTÉ (CI)
--------------------------------------------------------------------------------
Hôte / FQDN          : noc-zabbix-prod
Adresse IP           : 127.0.0.1 (Interface Agent VirtualBox)
OS                   : Ubuntu Server 22.04 LTS (Kernel Linux 5.15)
Rôle du serveur      : Serveur de production supervisé

--------------------------------------------------------------------------------
2. DESCRIPTION DE L'ALERTE ZABBIX
--------------------------------------------------------------------------------
Nom du Déclencheur   : Linux: Load average is too high (1m, 5m, 15m)
Sévérité Zabbix      : Warning / High
Heure Déclenchement  : 2026-11-25 15:35:12
Mon Acquittement     : OUI - Acquitté à 15:36:05 (MTTA = 53 secondes)
Commentaire consigné : "Incident pris en compte par Analyste NOC N1. Début 
                       des investigations CLI en SSH sur l'hôte noc-zabbix-prod."

--------------------------------------------------------------------------------
3. MES ACTIONS ET INVESTIGATIONS NIVEAU 1
--------------------------------------------------------------------------------
[✓] Test Réseau (L3) : ping 127.0.0.1 -> 0% perte, latence < 0.1ms (Nominal).
[✓] Test Ports (L4)  : Port 22 (SSH) et Port 10050 (Agent Zabbix) OUVERTS.
[✓] Démon Système    : zabbix-agent.service actif (running), PID 1420.
[!] Métrique CPU     : Load Average constaté à 2.85 (seuil de déclencheur > 1.50).
                       Processus identifiés en top CPU : dpkg / apt-get lors
                       de la phase de mise à jour des paquets du serveur.
[✓] Mémoire & Disque : RAM utilisée 1.1 Go / 3.8 Go (OK) - Disque / à 28% (OK).
[✓] Logs Consultés   : journalctl et /var/log/zabbix/zabbix_agentd.log analysés
                       -> Aucun crash démon, aucune trace d'intrusion.

--------------------------------------------------------------------------------
4. MON ANALYSE & MON HYPOTHÈSE N1
--------------------------------------------------------------------------------
La hausse temporaire du Load Average est directement liée à l'exécution de la mise
à jour système (concomitante avec l'alerte "Number of installed packages changed").
Aucun processus zombie n'a été constaté. La charge décroît progressivement.

--------------------------------------------------------------------------------
5. MOTIF DE MON ESCALADE & ATTENTE VIS-À-VIS DU N2
--------------------------------------------------------------------------------
Validation par l'équipe N2 de l'ajustement éventuel du seuil de sensibilité du
trigger de Load Average dans le template Zabbix, ou autorisation de clôture 
du ticket suite au retour à la normale constaté.

--------------------------------------------------------------------------------
MES PIÈCES JOINTES & PREUVES D'EXPLOITATION
--------------------------------------------------------------------------------
- Capture dashboard : ./screenshots/phase3-02-dashboard-completed.png
- Capture alerte ZBX: ./screenshots/phase4-01-alert-acknowledged.png
- Capture résolution: ./screenshots/phase4-02-alert-resolved.png
================================================================================
```

---
*Ce playbook constitue ma référence opérationnelle au quotidien pour assurer un traitement rigoureux et professionnel des incidents au sein d'une équipe NOC.*
