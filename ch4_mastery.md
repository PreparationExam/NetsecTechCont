# CH4 — MASTERY FILE : Systèmes de Détection et de Prévention d'Intrusion (IDS/IPS)

---

## CHAPITRE CORE

**Espace problématique :** Un pare-feu filtre le trafic selon des règles statiques (port, adresse IP) mais **n'inspecte pas le contenu** du trafic autorisé. Un attaquant peut envoyer du contenu malveillant sur le port 80 (HTTP) sans jamais déclencher le pare-feu. L'IDS/IPS comble ce vide en analysant le contenu réel des paquets, les comportements anormaux, et les déviations de protocole — à l'intérieur du périmètre défendu par le pare-feu.

**Pourquoi ça compte :** C'est la couche de sécurité qui distingue une défense en profondeur (defense-in-depth) d'une sécurité périphérique naive. Sans IDS/IPS, une intrusion réussie à travers le pare-feu est invisible jusqu'à ce que des dommages soient constatés.

---

## CONCEPTS

---

### 1. IDS — Intrusion Detection System

**Définition académique :**
Un système de détection d'intrusion (IDS) est un dispositif de sécurité réseau qui surveille le trafic réseau entrant et sortant, identifie les schémas suspects indicatifs d'une violation de la politique de sécurité, génère des alertes et enregistre les événements, sans intervenir directement sur le flux de données.

**Mécanisme :**
1. Les capteurs réseau (network sensors) capturent les paquets circulant sur le réseau.
2. L'analyseur compare les paquets aux signatures de la base de données OU aux profils de comportement normal.
3. En cas de correspondance ou déviation, une alerte est générée.
4. L'alerte est transmise à la console de commande, journalisée, et envoyée à l'administrateur.
5. L'IDS **n'intervient pas** sur le flux — il observe en parallèle (mode passif par défaut).

**Déploiement :** En parallèle (out-of-band), via un **network tap** ou un port miroir (SPAN port). Le trafic ne passe pas par l'IDS.

**Actions possibles d'un IDS :**
- Afficher une alerte dans l'interface
- Enregistrer l'événement dans un journal (log)
- Envoyer une notification à l'administrateur (email, pop-up, SMS)
- Reconfigurer le réseau pour atténuer les conséquences d'une intrusion

**Ce qu'un IDS NE FAIT PAS :** Il ne bloque pas le trafic en temps réel.

**Piège d'examen classique :** L'IDS peut "reconfigurer le réseau" — ceci est un mode actif (Active IDS). Un IDS passif se contente d'alerter. Ne pas confondre.

---

### 2. IPS — Intrusion Prevention System

**Définition académique :**
Un système de prévention d'intrusion (IPS) est une extension de l'IDS déployée en ligne (in-line) dans le chemin direct du trafic réseau, capable non seulement de détecter les activités malveillantes mais aussi d'y répondre activement en temps réel, notamment en bloquant ou modifiant le trafic suspect.

**Mécanisme :**
1. Tout le trafic passe obligatoirement à travers l'IPS (in-line).
2. L'IPS analyse chaque paquet.
3. Si malveillant → blocage immédiat, drop du paquet, réinitialisation de connexion.
4. Si légitime → transmission au réseau interne.

**Capacités spécifiques d'un IPS :**
- Envoyer des alertes
- Défragmenter les flux de paquets
- Supprimer les paquets malveillants identifiés
- Réduire les problèmes de séquencement TCP
- Réinitialiser une connexion (TCP reset)
- Bloquer le trafic provenant d'une adresse IP malveillante
- Corriger les erreurs CRC (Cyclic Redundancy Check)
- Nettoyer les options inutiles des couches transport et réseau

**Risque critique :** Déploiement in-line = point de défaillance unique. Un IPS mal configuré peut causer une interruption de service (DoS involontaire par faux positifs).

---

### 3. Différence IDS vs IPS — Mode d'implémentation

| Critère | IDS | IPS |
|---|---|---|
| Position dans le réseau | En parallèle (out-of-band) | En ligne (in-line) |
| Mode de connexion | Network tap / port miroir | Dans le chemin direct du trafic |
| Action sur le trafic | Aucune (surveillance passive) | Bloque ou autorise selon politique |
| Capacité de blocage | Non (mode passif) | Oui (temps réel) |
| Risque de faux positif | Alerte non pertinente | Blocage de trafic légitime |
| Impact en cas de défaillance | Aucun sur le flux réseau | Possible interruption du réseau |
| Intégration | Après le pare-feu, en dérivation | Entre pare-feu et réseau interne |

**⚠️ Piège d'examen :** L'IPS est une **extension** de l'IDS, pas un système distinct. Un IPS fait tout ce que fait un IDS, PLUS l'action préventive.

---

### 4. IDS vs Pare-feu

**Différence fondamentale :**
- Le pare-feu filtre selon des règles (port, adresse IP, protocole) et opère au niveau des couches 3-4. Il **n'inspecte pas le contenu** du trafic autorisé.
- L'IDS est placé **derrière** le pare-feu et inspecte le contenu des paquets qui ont déjà passé le pare-feu. Il surveille l'intérieur du réseau, incluant les menaces internes.
- Le pare-feu bloque mais n'alerte pas. L'IDS alerte mais ne bloque pas (en mode passif).

**Exemple concret du cours :** Un pare-feu autorise le port 80 (HTTP) et le port 25 (SMTP). Un attaquant injecte une charge malveillante dans une requête HTTP → le pare-feu laisse passer. L'IDS analyse le contenu et détecte la signature d'attaque.

---

### 5. Les trois méthodes de détection d'intrusion

#### 5a. Reconnaissance par signatures (Signature-based / Misuse Detection)

**Définition académique :**
La reconnaissance par signatures, également appelée détection par mauvaise utilisation (misuse detection), est une méthode qui compare le trafic réseau analysé à une base de données de signatures d'attaques connues (patterns binaires prédéfinis), et déclenche une alerte lorsqu'une correspondance est trouvée.

**Mécanisme :**
1. Une base de signatures est maintenue et mise à jour.
2. Chaque paquet entrant est comparé aux signatures via du pattern matching.
3. Correspondance trouvée → connexion coupée, paquet supprimé (drop), log enregistré, alarme déclenchée.
4. Pas de correspondance → paquet transmis à l'étape suivante (détection d'anomalies).

**Avantages :**
- Faible taux de faux positifs (règles précises)
- Détection rapide des attaques connues
- Identification précise de l'outil ou technique utilisée

**Inconvénients :**
- Ne détecte **pas** les attaques inconnues (zero-day)
- La base de signatures doit être **continuellement mise à jour**
- Un volume élevé de signatures consomme de la bande passante et peut causer des pertes de paquets
- Signatures trop strictes → variantes d'attaques non détectées

**Composants architecturaux :**
- Auditing Modules
- Profiles
- **Interference Engine** (moteur d'inférence → compare et décide)

**⚠️ Piège :** "Signatures strictes = peu de faux positifs mais aussi peu de flexibilité face aux variantes". Ne pas confondre la précision avec la couverture.

---

#### 5b. Détection d'anomalies (Anomaly-based Detection)

**Définition académique :**
La détection d'anomalies est une méthode qui établit un profil statistique du comportement normal du réseau et des utilisateurs, puis identifie toute déviation significative par rapport à ce profil comme une intrusion potentielle.

**Mécanisme :**
1. Phase d'apprentissage : l'IDS observe le trafic sur une période définie pour établir une baseline (comportement normal).
2. La baseline inclut : volume moyen d'emails, taux d'échecs d'authentification, utilisation CPU, bande passante normale, etc.
3. En exploitation : toute déviation au-delà d'un seuil de tolérance est signalée comme anomalie.
4. Anomalie détectée → connexion coupée, paquet supprimé, log, alarme.
5. Pas d'anomalie → passage à l'analyse d'état du protocole (stateful protocol analysis).

**Avantages :**
- Détecte des **attaques inconnues** (zero-day, nouvelles variantes)
- Les informations collectées peuvent enrichir les signatures des IDS basés sur les signatures
- Alertes précoces (détecte les sondes/probes)

**Inconvénients :**
- **Taux élevé de faux positifs** — comportement légitime inhabituel détecté comme attaque
- Construction du modèle de comportement normal est difficile et imprécise
- Le trafic réseau est imprévisible → modèles approximatifs
- Un modèle unique ne convient pas à tous les environnements réseau

**Techniques utilisées :** IA, réseaux de neurones, data mining, méthodes statistiques.

**Composants architecturaux :**
- Auditing Modules
- Profiles
- **Anomaly Detection Engine** (remplace l'Interference Engine)

**⚠️ Piège :** "Anomaly-based détecte zero-day, signature-based ne le fait pas" — c'est LA distinction la plus testée en examen.

---

#### 5c. Analyse d'état du protocole (Stateful Protocol Analysis)

**Définition académique :**
L'analyse d'état du protocole est une méthode de détection qui identifie les déviations par rapport au comportement attendu d'un protocole réseau, en comparant les événements observés à des profils prédéfinis par les fournisseurs décrivant l'utilisation normale de chaque protocole à différentes couches (réseau, transport, application).

**Mécanisme :**
1. Des profils de comportement normal sont définis pour chaque protocole (basés sur les RFC).
2. L'IDS analyse l'état de la communication protocolaire.
3. Détection de : séquences de commandes imprévues, répétitions anormales, commandes arbitraires, longueurs de commandes suspectes.
4. Pour les protocoles avec authentification : l'IDS trace l'identifiant de session.
5. Déviation détectée → connexion coupée, paquet supprimé, log, alarme.
6. Pas de déviation → paquet transmis au réseau via switch.

**Spécificité :** Cette méthode se distingue de la détection d'anomalies en ce qu'elle est spécifique aux **anomalies de protocole** (violations des RFC), pas aux anomalies comportementales générales.

**⚠️ Piège :** La stateful protocol analysis peut détecter de nouvelles attaques (comme la détection d'anomalies) car elle ne se base pas sur des signatures connues, mais sur des déviations de spécifications RFC. Cependant, les opérateurs IDS doivent avoir une connaissance approfondie des protocoles pour interpréter les alertes correctement.

---

### 6. Système de Détection d'Abus vs Système de Détection d'Anomalies

| Critère | Détection d'Abus (Misuse) | Détection d'Anomalies |
|---|---|---|
| Approche | Statique — règles prédéfinies | Dynamique — baseline comportementale |
| Faux positifs | Faible | Élevé |
| Détection zero-day | Non | Oui |
| Composant clé | Interference Engine | Anomaly Detection Engine |
| Mise à jour nécessaire | Oui (signatures) | Oui (modèle de comportement) |
| Précision | Plus précise sur attaques connues | Moins précise, plus générale |

---

### 7. Classification des IDS

Un IDS est classé selon **6 axes** :

#### Axe 1 : Approche de détection
- **Détection par signatures** (Signature-based)
- **Détection par anomalies** (Anomaly-based)

#### Axe 2 : Système protégé (Protection-based)
- **NIDS** (Network IDS) : protège le réseau
- **HIDS** (Host IDS) : protège un hôte
- **IDS Hybride** : protège réseau + hôtes

#### Axe 3 : Structure
- **IDS Centralisé** : toutes les données transmises vers un point central pour analyse
- **IDS Distribué** : plusieurs IDS communiquent entre eux, analyse locale + partage d'alertes

#### Axe 4 : Source de données
- **Journaux d'audit (Audit Trails)** : détecte problèmes de performance, violations de sécurité, failles applicatives
- **Paquets réseau** : capture et analyse de paquets pour détecter attaques connues

#### Axe 5 : Comportement après attaque
- **IDS Actif (Active IDS)** : détecte ET répond automatiquement, sans intervention humaine
- **IDS Passif (Passive IDS)** : détecte uniquement, alerte l'administrateur, intervention humaine requise

#### Axe 6 : Temps d'analyse
- **IDS en temps réel (On-the-fly)** : analyse continue, flux d'information constant entre capteurs et moteurs d'analyse
- **IDS à analyse périodique (Interval-based)** : données stockées puis analysées hors ligne (offline)

---

### 8. NIDS — Network-based IDS

**Définition :** Un NIDS surveille et analyse le trafic réseau sur un segment ou l'ensemble du réseau, en analysant les paquets et protocoles réseau, déployé aux frontières du réseau.

**Caractéristiques :**
- Surveille le trafic réseau global
- Analyse les paquets et protocoles
- Détecte les activités suspectes sur un segment réseau
- Placé aux frontières (derrière pare-feu, routeurs, VPN)
- Protège l'ensemble du réseau
- **Limitation :** Ne surveille pas directement les hôtes individuels

---

### 9. HIDS — Host-based IDS

**Définition :** Un HIDS est installé sur un hôte spécifique et surveille l'activité de cet hôte : trafic réseau local, journaux, processus, applications, accès et modifications de fichiers.

**Caractéristiques :**
- Installé sur chaque hôte critique
- Surveille : logs, processus, applications, accès fichiers
- Protège des données très sensibles
- Utilisé sur serveurs exposés
- **Limitation :** Ne voit pas le trafic global du réseau

---

### 10. IDS Hybride

**Définition :** Combine NIDS + HIDS pour une couverture complète.

**Avantages :**
- Faible taux de faux positifs du NIDS
- Détection d'attaques inconnues de l'anomaly-based (HIDS)
- Fonctionne sur réseaux chiffrés (car HIDS analyse l'hôte, pas le flux chiffré)
- Centralise les données sur un serveur unique
- **Inconvénient :** Plus complexe à gérer

**Architecture :** Known Attack → Misuse Detection → Unknown Features → Anomaly Detection → Novel Attack

---

### 11. IDS Actif vs IDS Passif

| Critère | IDS Actif | IDS Passif |
|---|---|---|
| Action | Détecte ET répond | Détecte seulement |
| Blocage automatique | Oui, sans intervention humaine | Non |
| Contre-mesures | Appliquées en temps réel | Requièrent intervention humaine |
| Notification | Alerte + action | Alerte uniquement (email, pop-up) |
| Équivalent fonctionnel | Proche d'un IPS | IDS classique |

---

### 12. IDS Centralisé vs IDS Distribué

| Critère | Centralisé | Distribué |
|---|---|---|
| Analyse | Point central unique | Locale, avec partage d'alertes |
| Point unique de défaillance | Oui | Non |
| Scalabilité | Limitée | Excellente |
| Résistance aux attaques distribuées (DDoS) | Faible | Bonne |
| Complexité de gestion | Simple | Plus complexe |
| Vision globale | Oui (console IDS centrale) | Partielle (chaque IDS voit son segment) |

**Architecture centralisée :** Console IDS centrale qui corrèle toutes les alertes, génère des alertes globales, aide à la prise de décision.

---

### 13. IDS Temps Réel vs Périodique

| Critère | Temps Réel (On-the-fly) | Périodique (Interval-based) |
|---|---|---|
| Flux d'information | Continu | Discontinu, stocké puis transmis |
| Analyse | À la volée | Hors ligne (offline) |
| Réactivité | Immédiate | Différée |
| Usage typique | Environnements critiques | Audit, forensic |

---

### 14. Composants d'un IDS

Un IDS est constitué de **5 composants principaux** :

1. **Capteurs réseau (Network Sensors)** : Composants matériels/logiciels qui surveillent le trafic et déclenchent des alertes. Placés aux points d'entrée : passerelles Internet, connexions LAN, serveurs d'accès distant, dispositifs VPN, de part et d'autre d'un pare-feu.

2. **Analyseur (Analyzer)** : Examine et interprète les données collectées par les capteurs.

3. **Systèmes d'alerte (Alert Systems)** : Déclenchent des alertes quand une activité malveillante est détectée. Canaux : fenêtres pop-up, emails, signaux sonores, notifications mobiles/SMS.

4. **Console de commande (Command Console)** : Interface entre l'administrateur et l'IDS. **Doit être installée sur un système dédié** — si installée sur un pare-feu ou serveur de sauvegarde, ralentissement significatif de la réponse aux incidents. Logiciel de référence : **Sguil**.

5. **Système de réponse (Response System)** : Met en œuvre les contre-mesures. L'administrateur ne doit **pas** se reposer uniquement dessus — intervention humaine toujours nécessaire pour gérer les faux positifs et décider de l'escalade.

6. **Base de signatures d'attaque (Attack Signature Database)** : Base de données de signatures et modèles d'attaque. L'IDS compare le trafic à cette base. **Doit être mise à jour régulièrement.**

---

### 15. Types d'alertes IDS

| Type | Situation | Commentaire |
|---|---|---|
| **Vrai Positif (True Positive)** | Attaque réelle → Alerte déclenchée | Fonctionnement idéal |
| **Faux Positif (False Positive)** | Pas d'attaque → Alerte déclenchée | Trafic normal interprété comme attaque |
| **Faux Négatif (False Negative)** | Attaque réelle → Pas d'alerte | **La défaillance la plus dangereuse** |
| **Vrai Négatif (True Negative)** | Pas d'attaque → Pas d'alerte | Fonctionnement normal |

**Impact des faux positifs :** Rendent les utilisateurs moins sensibles aux alertes → "alert fatigue" → réaction atténuée face à de vraies intrusions.

**Impact des faux négatifs :** La fonction principale d'un IDS est de détecter les attaques. Un faux négatif = échec total de la mission.

**⚠️ Piège d'examen classique :** "Quel est le type d'alerte le plus dangereux ?" → **Faux négatif** (l'attaque passe inaperçue). Ne pas confondre avec faux positif (moins dangereux mais problématique).

---

### 16. Indications d'intrusion

**Intrusions dans le système de fichiers :**
- Nouveaux fichiers ou programmes inconnus
- Élévation de privilèges inexpliquée (modification des permissions)
- Modifications inexpliquées de la taille des fichiers
- Fichiers suid/sgid suspects (Linux)
- Noms de fichiers inhabituels (doubles extensions)
- Absence ou disparition de fichiers

**Intrusions réseau :**
- Augmentation soudaine de la consommation de bande passante
- Sondages répétés des services disponibles
- Connexions depuis des adresses IP extérieures à la plage réseau
- Tentatives répétées de connexion depuis des hôtes distants
- Augmentation soudaine des logs (peut indiquer DoS/DDoS)

**Intrusions système :**
- Changements soudains dans les journaux (logs courts ou incomplets)
- Ralentissement inhabituel des performances
- Journaux disparus ou avec permissions incorrectes
- Modifications des logiciels systèmes ou fichiers de configuration
- Affichages graphiques ou messages textuels anormaux
- Processus inhabituels, redémarrages inattendus

---

### 17. Ce qu'un IDS/IPS N'EST PAS

Un IDS/IPS **ne peut pas remplacer** :
- **Systèmes de journalisation réseau** : qui détectent des vulnérabilités de type DoS sur réseau congestionné
- **Outils d'évaluation de vulnérabilités** : scanners qui recherchent bogues et failles dans OS et services réseau
- **Produits antivirus** : qui traitent virus, chevaux de Troie, vers, bactéries logicielles, bombes logiques
- **Systèmes de sécurité/cryptographiques** : VPN, SSL, S/MIME, Kerberos, RADIUS

**⚠️ Piège :** Un étudiant pourrait penser qu'un IDS avec détection par anomalies remplace un antivirus. **Faux.** Les rôles sont distincts.

---

### 18. Erreurs courantes de configuration IDS/IPS

- Déployer l'IDS à un emplacement où il ne voit pas tout le trafic réseau
- Ignorer fréquemment les alertes générées
- Ne pas disposer d'une politique de réponse adéquate
- Ne pas ajuster l'IDS pour réduire les faux négatifs et faux positifs
- Ne pas mettre à jour l'IDS avec les dernières signatures du vendeur
- Surveiller uniquement les connexions entrantes (oublier le trafic sortant)

---

### 19. Étapes de la détection d'intrusion (7 étapes)

1. **Installer les signatures** dans la base de données (avant tout paquet)
2. **Collecter les données** via les capteurs réseau
3. **Envoyer un message d'alerte** quand un paquet correspond à une signature ou dévie du comportement normal
4. **L'IDS réagit** : pop-up, email, ou réponse automatique si configurée
5. **L'administrateur évalue les dommages** : décide si contre-mesures nécessaires
6. **Procédures d'escalade** si vrai positif confirmé (selon gravité)
7. **Journalisation et révision** : mise à jour des signatures, préparation aux futures attaques

---

### 20. Déploiement d'un NIDS — 4 emplacements

| Emplacement | Position | Avantages |
|---|---|---|
| **1** | Derrière pare-feu externe / dans la DMZ | Surveille attaques externes, révèle failles du pare-feu, détecte attaques sur serveurs Web/FTP DMZ, surveille trafic sortant de serveurs compromis |
| **2** | À l'extérieur du pare-feu externe | Identifie nombre et types d'attaques venant d'Internet |
| **3** | Sur les dorsales réseau (network backbones) | Volume élevé de trafic analysé, détection tentatives non autorisées de l'extérieur |
| **4** | Sur les sous-réseaux critiques (critical subnets) | Focus sur ressources et systèmes critiques |

---

### 21. Outils IDS/IPS notables

| Outil | Type | Caractéristiques |
|---|---|---|
| **Snort** | NIDS open source | Analyse temps réel, pattern matching, détecte buffer overflows, stealth port scans, OS fingerprinting, langage de règles flexible, architecture plug-ins |
| **Suricata** | NIDS/IPS/NSM | IDS temps réel + IPS inline + NSM + traitement PCAP offline |
| **OSSEC** | HIDS open source | Alertes HIDS, analyse de logs |
| **Zeek** (ex-Bro) | NSM | Surveillance de la sécurité réseau |
| **AlienVault OSSIM** | SIEM + IDS | Corrélation centralisée |
| **SolarWinds SEM** | SIEM | Gestion événements de sécurité |

---

### 22. Caractéristiques d'une bonne solution IDS

1. Fonctionne en continu avec intervention humaine minimale
2. Tolérant aux pannes (fault-tolerant)
3. Résistant au contournement (anti-subversion)
4. Surcharge minimale sur le système
5. Observe les écarts par rapport au comportement normal
6. Difficile à tromper (fonction de vérification des fichiers)
7. Adapté aux besoins spécifiques du système
8. Capable de s'adapter au comportement dynamique du système
9. Fonctionnalité de dissimulation (fail-safe / honeypot) dans les grandes organisations

---

### 23. Sélection d'une solution IDS/IPS

Critères d'évaluation :
- **Exigences générales** : taille de l'organisation, volume de trafic
- **Exigences de sécurité** : environnement, politiques, infrastructure actuelle
- **Exigences de performance** : capacité à gérer le trafic (NIDS) ou les événements (HIDS)
- **Exigences de gestion** : conformité à la politique de gestion organisationnelle
- **Coûts du cycle de vie** : budget disponible

---

## TABLEAUX DE COMPARAISON

### IDS vs IPS vs Pare-feu

| Critère | Pare-feu | IDS | IPS |
|---|---|---|---|
| Position | Périmètre réseau | Derrière pare-feu, parallèle | Derrière pare-feu, in-line |
| Action sur trafic | Filtre (bloque/autorise) | Aucune action | Bloque/autorise/modifie |
| Inspection contenu | Non | Oui | Oui |
| Alerte | Non | Oui | Oui |
| Blocage temps réel | Oui (niveau règles) | Non | Oui |
| Détection attaques internes | Non | Oui | Oui |
| Impact si défaillance | Réseau exposé | Aucun sur flux | Possible interruption |

### Signature-based vs Anomaly-based vs Stateful Protocol Analysis

| Critère | Signature-based | Anomaly-based | Stateful Protocol |
|---|---|---|---|
| Base de référence | Signatures d'attaques connues | Baseline comportementale | Spécifications RFC des protocoles |
| Détection zero-day | Non | Oui | Oui (si viole RFC) |
| Taux de faux positifs | Faible | Élevé | Moyen |
| Mise à jour nécessaire | Signatures | Modèle comportemental | Profils de protocole |
| Atout principal | Précision sur attaques connues | Nouvelles attaques | Violations de protocoles |
| Limite principale | Attaques inconnues invisibles | Alert fatigue | Connaissance protocoles requise |

### NIDS vs HIDS vs Hybrid

| Critère | NIDS | HIDS | Hybride |
|---|---|---|---|
| Protège | Réseau | Hôte individuel | Réseau + Hôtes |
| Niveau d'analyse | Global (segments réseau) | Local (machine) | Global + Local |
| Voit trafic chiffré | Non | Oui (déchiffré à l'hôte) | Oui |
| Faux positifs | Moins | Plus | Optimisé |
| Détection attaques inconnues | Moins | Mieux (anomaly-based) | Optimal |
| Complexité | Simple | Simple par hôte | Complexe |

### Vrai Positif / Faux Positif / Faux Négatif / Vrai Négatif

| Type | Attaque ? | Alerte ? | Dangereux ? |
|---|---|---|---|
| Vrai Positif | Oui | Oui | Non (idéal) |
| Faux Positif | Non | Oui | Moyen (alert fatigue) |
| **Faux Négatif** | **Oui** | **Non** | **OUI — le plus dangereux** |
| Vrai Négatif | Non | Non | Non (idéal) |

---

## QUESTIONS OUVERTES + RÉPONSES MODÈLES

---

### Q1 : Expliquez pourquoi un pare-feu seul ne suffit pas à sécuriser un réseau et quel rôle joue un IDS en complément.

**Réponse modèle :**
Un pare-feu est un dispositif de filtrage opérant principalement aux couches réseau et transport (couches 3 et 4 du modèle OSI). Il applique des règles prédéfinies basées sur les adresses IP, les numéros de port et les protocoles pour autoriser ou bloquer le trafic. Sa limitation fondamentale est qu'il **n'inspecte pas le contenu applicatif** du trafic autorisé. Ainsi, un trafic apparemment légitime (par exemple, une requête HTTP sur le port 80 ou un email sur le port 25) peut transporter une charge malveillante sans déclencher le pare-feu.

Un IDS comble cette lacune en opérant **derrière le pare-feu**, à l'intérieur du réseau protégé. Il analyse le contenu effectif des paquets qui ont déjà franchi le périmètre pare-feu, à la recherche de signatures d'attaques connues, d'anomalies comportementales ou de déviations par rapport aux spécifications de protocole. L'IDS adopte le principe de **défense en profondeur** (defense-in-depth), en ajoutant une couche d'analyse qui complète sans remplacer le pare-feu. De plus, contrairement au pare-feu, l'IDS surveille également les menaces internes — l'IDS agit à l'intérieur du réseau et peut détecter des comportements suspects provenant d'utilisateurs internes, ce qu'un pare-feu périmétrique ne peut pas faire.

**Rubrique :** Points complets → mention des couches OSI du pare-feu, explication de l'inspection de contenu, positionnement derrière le pare-feu, defense-in-depth, menaces internes. Perte de points si : affirmation que l'IDS remplace le pare-feu ou que l'IDS bloque le trafic (confusion IDS/IPS).

---

### Q2 : Comparez la détection par signatures et la détection par anomalies. Dans quel cas préféreriez-vous l'une à l'autre ?

**Réponse modèle :**
La **détection par signatures** (signature-based ou misuse detection) compare le trafic réseau à une base de données de patterns d'attaques connus. Elle offre un faible taux de faux positifs car les règles sont précises et prédéfinies. Son principal inconvénient est l'incapacité à détecter des attaques inconnues (zero-day) et la nécessité de maintenir la base de signatures continuellement à jour.

La **détection par anomalies** (anomaly-based) établit une baseline statistique du comportement normal du réseau, puis signale toute déviation comme potentiellement malveillante. Elle est capable de détecter des attaques inconnues mais génère un taux élevé de faux positifs en raison de l'imprévisibilité inhérente du comportement réseau légitime.

**Préférence situationnelle :**
- Environnement avec attaques connues et bien documentées, faible tolérance aux faux positifs → **signature-based**
- Environnement à risque zero-day élevé, capable d'absorber et d'analyser de nombreuses alertes, nécessitant une détection proactive → **anomaly-based**
- En pratique, les IDS hybrides combinent les deux approches : la détection par signatures traite le volume des attaques connues avec précision, l'anomaly-based assure la couverture des nouvelles menaces.

**Rubrique :** Points complets → définition précise des deux méthodes, mention zero-day, taux de faux positifs comparés, justification situationnelle, mention possible d'approche hybride. Perte de points si : confusion entre les deux méthodes, ou affirmation qu'une méthode est universellement supérieure.

---

### Q3 : Décrivez les 4 types d'alertes qu'un IDS peut générer et expliquez lequel est le plus critique pour la sécurité du réseau.

**Réponse modèle :**
Un IDS peut générer quatre types d'alertes selon la matrice attaque/alerte :

**Vrai Positif (True Positive)** : L'IDS déclenche une alerte lors d'une attaque réelle. C'est le comportement attendu et idéal du système.

**Faux Positif (False Positive)** : L'IDS déclenche une alerte alors qu'aucune attaque n'est en cours — l'IDS interprète incorrectement une activité légitime comme malveillante. Ce phénomène peut provoquer une "alert fatigue" chez les administrateurs, les rendant moins réactifs aux alertes réelles.

**Faux Négatif (False Negative)** : L'IDS ne déclenche **aucune** alerte lors d'une attaque réelle. C'est la **défaillance la plus critique**, car elle prive l'organisation de toute visibilité sur une intrusion en cours. La mission principale d'un IDS étant la détection, un faux négatif constitue un échec systémique complet.

**Vrai Négatif (True Negative)** : L'IDS ne déclenche pas d'alerte lorsqu'aucune attaque ne se produit. C'est un comportement correct, correspondant au bon fonctionnement normal du système.

Le type le plus dangereux est sans conteste le **faux négatif** : une attaque réelle passe inaperçue, permettant à l'intrus de compromettre le réseau sans être détecté. Tandis que les faux positifs surchargent les administrateurs, ils ne compromettent pas directement la sécurité. Les faux négatifs, eux, annulent l'utilité même du système.

**Rubrique :** Points complets → définition des 4 types avec leur notation (Attack/No Attack × Alert/No Alert), justification de la criticité du faux négatif. Perte de points si : désignation du faux positif comme le plus dangereux.

---

### Q4 : Quels sont les critères de classification d'un IDS ? Présentez les principales catégories avec leurs caractéristiques.

**Réponse modèle :**
Un IDS peut être classifié selon six critères principaux :

**1. Approche de détection** : Signature-based (détection d'attaques connues par pattern matching) vs Anomaly-based (détection par déviation comportementale).

**2. Système protégé** : Le NIDS (Network IDS) surveille le trafic réseau global depuis des points stratégiques du réseau. Le HIDS (Host IDS) est installé sur chaque hôte et surveille l'activité locale (logs, processus, fichiers). L'IDS hybride combine les deux.

**3. Structure** : L'IDS centralisé agrège toutes les données vers un point d'analyse central, offrant une vision globale mais créant un point unique de défaillance. L'IDS distribué déploie plusieurs capteurs qui communiquent entre eux, sans point unique de défaillance et avec une meilleure scalabilité.

**4. Source de données** : Journaux d'audit (audit trails) pour détecter violations de sécurité et anomalies applicatives, ou capture de paquets réseau pour la détection d'attaques réseau connues.

**5. Comportement après attaque** : L'IDS actif détecte et répond automatiquement (sans intervention humaine). L'IDS passif détecte seulement et notifie l'administrateur qui doit intervenir manuellement.

**6. Temps d'analyse** : L'IDS temps réel analyse le flux en continu (on-the-fly). L'IDS périodique (interval-based) stocke les données et les analyse hors ligne, avec une détection différée.

**Rubrique :** Points complets → mention des 6 axes de classification, définitions correctes pour chaque catégorie. Perte de points si : moins de 4 axes mentionnés, ou confusion NIDS/HIDS.

---

## LIENS ENTRE CHAPITRES

- **Pare-feu (chapitre précédent)** : L'IDS complète le pare-feu — ils ne se substituent pas. La combinaison pare-feu + IDS + IPS constitue la défense en profondeur.
- **Cryptographie / VPN** : Un IDS basé réseau (NIDS) ne peut pas inspecter le trafic chiffré (SSL/TLS, VPN). L'IDS hybride avec HIDS compense car l'hôte reçoit les données déchiffrées.
- **SIEM** : Un IDS envoie ses alertes et logs vers un SIEM (Security Information and Event Management) pour corrélation centralisée.
- **Honeypot** : Mentionné comme mécanisme de diversion dans les capacités avancées d'un IDS — lien avec les techniques de déception.
- **Forensic / Post-incident** : Les journaux IDS sont cruciaux pour les enquêtes de cyber-criminalistique (forensic) et l'installation de correctifs post-attaque.
