# 🔥 NETWORK SECURITY — EXAM MASTERPIECE
### Chapitres 2, 3, 4, 5, 6, 7, 9 — Synthèse Expert-Level

---

## TABLE DES MATIÈRES

1. [Mental Models & Architecture Globale](#1-mental-models--architecture-globale)
2. [CH2 — Segmentation, DMZ, Zero Trust](#2-ch2--segmentation-dmz-zero-trust)
3. [CH3 — Firewalls](#3-ch3--firewalls)
4. [CH4 — IDS / IPS](#4-ch4--ids--ips)
5. [CH5 — Honeypot](#5-ch5--honeypot)
6. [CH6 — Proxy Server](#6-ch6--proxy-server)
7. [CH7 — VPN](#7-ch7--vpn)
8. [CH9 — Load Balancer](#8-ch9--load-balancer)
9. [Carte d'intégration cross-chapitres](#9-carte-dintégration-cross-chapitres)
10. [Distinctions Expert — Les pièges qui font la différence](#10-distinctions-expert--les-pièges-qui-font-la-différence)
11. [Analyse des surfaces d'attaque & failure modes](#11-analyse-des-surfaces-dattaque--failure-modes)
12. [25 Questions d'Examen de Niveau Avancé](#12-25-questions-dexamen-de-niveau-avancé)
13. [Matrices de décision rapide](#13-matrices-de-décision-rapide)
14. [Misconception Graveyard](#14-misconception-graveyard)

---

## 1. Mental Models & Architecture Globale

### Le modèle fondamental : Defense-in-Depth

Chaque chapitre est une **couche de ce modèle**. L'erreur fatale en examen est de traiter chaque technologie isolément — elles forment un pipeline.

```
INTERNET
   │
[External Firewall]  ← Ch3 : filtre L3-L4 (paquets)
   │
[Load Balancer]      ← Ch9 : distribue + absorbe DDoS
   │
   DMZ
   ├── [Proxy Server] ← Ch6 : inspection L7, masque topologie interne
   ├── [Honeypot]     ← Ch5 : leurre, threat intelligence, zéro faux positifs
   ├── [Bastion Host] ← Ch2 : point d'accès durci unique
   └── [Serveurs Web / Mail / DNS]
   │
[IDS/IPS]            ← Ch4 : inspecte le CONTENU (ce que le FW laisse passer)
   │
[Internal Firewall]  ← Ch3 : segmente, protège l'intranet
   │
   INTRANET
   ├── [VPN Concentrateur] ← Ch7 : accès distant chiffré
   └── [Micro-segmentation / VLAN] ← Ch2 : contenu mouvement latéral
```

### Axiome numéro 1 : Chaque couche compense les failles de la précédente

- Le **Firewall** filtre les en-têtes mais **pas le contenu** → l'**IDS** prend le relais
- L'**IDS passif** alerte mais **ne bloque pas** → l'**IPS** prend le relais
- Le **VPN** chiffre → l'**IDS réseau** est aveugle → **HIDS** compense
- Le **Proxy** centralise → **SPOF** → **Load Balancer** compense

### Axiome numéro 2 : La position dans l'architecture détermine la capacité

| Position | Ce qu'on voit | Ce qu'on ne voit pas |
|----------|--------------|----------------------|
| Avant le FW externe | Tout le trafic Internet (brut) | Rien de l'intérieur |
| Dans la DMZ | Trafic entrant + sortant | Trafic interne LAN |
| Derrière le FW interne | Trafic interne uniquement | Trafic externe brut |
| Sur l'hôte (HIDS) | Trafic local déchiffré | Trafic global du réseau |

---

## 2. CH2 — Segmentation, DMZ, Zero Trust

### Core Mental Model

La segmentation est la réponse structurelle au **problème du mouvement latéral**. Un périmètre seul ne suffit pas — une fois à l'intérieur, un attaquant sur un réseau plat a tout.

### 2.1 Les trois types de segmentation — mécanismes exacts

#### Segmentation Physique
- Isolation via matériel dédié séparé (switches, câblage, firewalls par segment)
- **Aucun contournement logique possible** — c'est sa force ET sa limite
- Coût prohibitif pour les grandes infrastructures
- **Cas d'usage réel :** réseaux industriels (SCADA/ICS), salles serveurs classifiées

#### Segmentation Logique (VLAN — IEEE 802.1Q)
- Isolation L2 via étiquetage des trames Ethernet
- Un seul switch physique peut héberger des dizaines de VLANs
- **Inter-VLAN routing** nécessite un L3 switch ou routeur — c'est là que les ACLs s'appliquent
- **Surface d'attaque :** VLAN hopping via double-tagging (sur ports access mal configurés) ou exploitation des ports trunk

**VLAN Hopping — mécanisme précis :**
```
Attaquant envoie trame avec double-tag (outer VLAN = VLAN natif)
→ Switch 1 retire le premier tag → trame arrive au trunk avec inner tag
→ Switch 2 voit le VLAN de l'inner tag → délivre au VLAN cible
```
**Mitigation :** Changer le VLAN natif sur tous les trunks, désactiver les négociations DTP, ne jamais mettre d'hôtes sur le VLAN natif.

#### Virtualisation Réseau (NV)
- Abstraction complète des ressources réseau du hardware physique
- Crée des instances réseau compètes (overlay) sur une infrastructure partagée (underlay)
- **Différence clé vs VLAN :** les VLANs opèrent L2 sur infrastructure unique. NV crée des topologies L2-L7 complètes avec leur propre adressage, routage, politiques.
- **Capability unique :** plusieurs réseaux virtuels peuvent partager le même espace d'adressage IP sans conflit

### 2.2 Bastion Host — Architecture et hardening

**Propriétés non-négociables :**
- 2 interfaces réseau (publique ↔ privée)
- Aucun compte utilisateur (vecteur d'exploitation local)
- NFS désactivé
- Vérification d'intégrité par checksum (détecte modification de binaires)
- Double journalisation (une copie sur serveur dédié)
- Moniteurs automatisés (3 échecs login avec IDs différents = alerte)

**Position optimale :** Dans la DMZ, avec un packet filter supplémentaire entre le bastion et l'intranet — double couche.

**⚠️ Le bastion ne remplace PAS le firewall — ils sont complémentaires.**

### 2.3 DMZ — Règles de trafic canoniques

```
Internet → DMZ : AUTORISÉ (ports spécifiques : 80, 443, 25, 53)
DMZ → Intranet : BLOQUÉ (règle absolue)
Intranet → DMZ : AUTORISÉ (sauf exceptions)
Internet → Intranet : BLOQUÉ (absolu)
```

**Exception légitime DMZ→Intranet :** authentification Active Directory (le serveur web DMZ doit valider les credentials contre l'AD interne). Doit être explicitement autorisé sur les ports AD spécifiques uniquement.

### 2.4 DMZ Single FW vs Dual FW

| Critère | Single Firewall (3-legged) | Dual Firewall |
|---------|---------------------------|---------------|
| SPOF | OUI | NON |
| Interfaces | 3 (WAN / LAN / DMZ) | 2+2 |
| Sécurité | Modérée | Maximum |
| Si compromis | Toutes zones exposées | Seulement la zone entre les 2 FW |
| Coût/Complexité | Faible | Élevé |
| Bonne pratique HA | FW différents fabricants (évite vuln commune) | |

### 2.5 Trafic Nord-Sud vs Est-Ouest

**L'asymétrie critique :** Le trafic Est-Ouest est **plus volumineux** que Nord-Sud mais reçoit **moins d'attention sécurité historiquement**. C'est le vecteur de mouvement latéral.

| | Nord-Sud | Est-Ouest |
|-|----------|-----------|
| Direction | Vertical (externe ↔ serveur) | Horizontal (serveur ↔ serveur) |
| Volume | Moindre | Plus élevé |
| Couverture sécurité | Bonne (firewalls périmètre) | Historiquement faible |
| Threat | Attaquants externes | Mouvement latéral, insider threats |
| Solution | Firewall périmétrique | Micro-segmentation, SDN, internal FW |

### 2.6 Zero Trust — Architecture et distinctions critiques

**La phrase pivot du cours (mémoriser exactement) :** "Zero Trust ne protège pas le réseau, il protège les accès."

**Control Plane vs Data Plane :**
- **Control Plane :** coordonne, vérifie chaque demande d'accès, applique les politiques granulaires (rôle, heure, type appareil). Décide OUI ou NON.
- **Data Plane :** une fois approuvé par le Control Plane, configuré pour n'accepter le trafic QUE du client spécifique vérifié. Exécute la décision.

**Technologies ZT :** MFA + PAM + Micro-segmentation + Chiffrement (les 4 piliers)

**PAM (Privileged Access Management) — scope précis :** contrôle et audit des accès à privilèges élevés uniquement (admins, service accounts). Ne couvre PAS tous les utilisateurs.

---

## 3. CH3 — Firewalls

### Core Mental Model

Un firewall est un point d'application de politique (policy enforcement point). Sa valeur est directement proportionnelle à la qualité de sa politique — un FW mal configuré est pire qu'aucun FW (fausse sécurité).

### 3.1 Technologies de Firewall — Pipeline OSI

| Technologie | Layer | Stateful | Inspecte contenu | Vitesse | Sécurité |
|-------------|-------|----------|-------------------|---------|----------|
| Packet Filtering | L3-L4 | ❌ | ❌ | ★★★★★ | ★★ |
| Circuit-Level Gateway | L5 | Session only | ❌ | ★★★★ | ★★★ |
| Application Proxy | L7 | ✅ | ✅ Profond | ★★ | ★★★★★ |
| Stateful Multilayer | L3-L7 | ✅ | ✅ | ★★★ | ★★★★ |
| NGFW | L3-L7 | ✅ | ✅ DPI + chiffré | Variable | ★★★★★ |

### 3.2 Packet Filtering — Mécanisme exact et limites

**Sans état (stateless) :** chaque paquet évalué indépendamment. Ne sait pas si un paquet appartient à une session établie.

**Critères d'inspection :** IP src, IP dst, port src TCP/UDP, port dst TCP/UDP, flags TCP (SYN/ACK), protocole, direction, interface.

**Exploit direct :** IP spoofing — le FW croit que le paquet vient d'une IP de confiance. Pas de vérification de session pour détecter l'imposture.

**3 règles de configuration :**
1. Whitelist (accept only safe, deny all else)
2. Blacklist (deny only dangerous, accept all else)
3. Interactif (demande pour inconnus)

### 3.3 Circuit-Level Gateway — Mécanisme et propriété cachée

Opère L5 (couche session). Valide le TCP handshake (SYN→SYN-ACK→ACK). Ne lit **jamais** le contenu.

**Propriété clé souvent négligée :** Substitue son IP à celle du client → masque les infos du réseau interne. Le serveur cible voit l'IP du gateway, jamais l'IP interne.

**Usage réel :** N'est pas autonome — couplé avec packet filtering (L3) et application proxy (L7).

### 3.4 Application Proxy / Gateway — Différenciation critique

**Ce qu'il peut faire que les autres ne peuvent pas :**
- Filtrer des commandes HTTP spécifiques : `GET` autorisé, `DELETE` bloqué
- Détecter et bloquer les transferts FTP entrants
- Bloquer Telnet complètement même sur port 80
- Inspecter les commandes SMTP (bloquer VRFY, EXPN)

**Modèle de communication :** Double session — client ↔ proxy ET proxy ↔ serveur. Pas de connexion directe client-serveur.

**Fail mode :** Ne peut pas filtrer SSL/TLS chiffré sans SSL inspection configuré. Attaque possible : envelopper du payload malveillant dans HTTPS.

### 3.5 Stateful Multilayer — Pipeline de traitement (5 étapes)

```
Paquet entrant
    │
    ▼ Étape 1 : Table d'état
    Si appartient à session établie → AUTORISÉ direct
    Si nouvelle connexion → suite
    │
    ▼ Étape 2 : Règles L3 (packet filtering)
    Correspondance ? → action (accept/drop)
    Pas de correspondance → politique par défaut
    │
    ▼ Étape 3 : Vérification table d'état TCP
    Appartient à session active ? → oui : continue / non : BLOQUÉ
    │
    ▼ Étape 4 : Inspection L7 (application)
    Contenu conforme ? → oui : autorisé / non : bloqué
    │
    ▼ Transmet au réseau
```

**Avantage vs stateless :** Détecte les injections mid-session, les paquets hors session (SYN flood variants), les réponses sans requête correspondante.

### 3.6 NGFW — Ce qui le distingue

NGFW = Stateful + IPS intégré + DPI + Application Awareness + User Identity + SSL Inspection.

**Capacité critique :** Application Awareness — peut identifier l'application indépendamment du port. Un tunnel BitTorrent sur le port 443 sera détecté.

**User Identity Management :** politiques basées sur l'utilisateur/groupe AD, pas juste l'IP. Si IP change (DHCP), la politique suit l'utilisateur.

### 3.7 ACL — Distinction standard vs étendue

| ACL Standard | ACL Étendue |
|-------------|-------------|
| IP **source uniquement** | IP src + IP dst + MAC + Ports TCP/UDP |
| Traitement léger | Traitement plus lourd |
| Petits réseaux | Entreprise |

**Règle d'évaluation :** Top-down, première correspondance gagne. Les règles les plus spécifiques doivent être EN HAUT.

### 3.8 NAT — 5 schémas et leur trade-off sécurité

| Type NAT | Mappage | Économie IP | Usage |
|----------|---------|-------------|-------|
| Static 1:1 | 1 IP externe fixe par IP interne | ❌ | Serveurs nécessitant IP fixe |
| Dynamic address | Dynamique, pas de port | Partiel | Accès limité au nombre d'IP ext |
| NAPT/PAT | Plusieurs internes → 1 IP ext via ports | ✅ Maximum | Le plus courant |
| Dynamic addr+port | Paire IP+port dynamique | ✅ | Efficacité max |
| Destination NAT | IP routeur → serveur interne | N/A | Services exposés depuis extérieur |

**⚠️ NAT ≠ Firewall. NAT interfère avec IPsec** (IPsec vérifie l'intégrité des IPs — NAT les modifie → checksum invalide). Solution : NAT-T (NAT Traversal) encapsule IPsec dans UDP.

### 3.9 Limites du Firewall — Liste complète (critique examen)

Un FW **NE protège PAS contre :**
- Attaques par **backdoor** (employé interne + attaquant externe coordonnés)
- **Menaces internes** (insider threats)
- **Social engineering** (l'utilisateur initie lui-même la connexion)
- **Virus zero-day** (non connu → pas de règle)
- Trafic **tunnelisé** dans des protocoles autorisés (HTTPS sur port 443 avec payload malveillant)
- Attaques via **ports courants** (port 80, 443 — souvent autorisés)
- **Mauvaise conception réseau** (ne compense pas les erreurs d'architecture)
- **Remote access** (modems, VPN non sécurisés)
- Attaques sur **couches supérieures** de la pile protocolaire

---

## 4. CH4 — IDS / IPS

### Core Mental Model

L'IDS/IPS est la couche qui pallie la cécité applicative du firewall. Le firewall autorise le port 80 → l'IDS inspecte CE QUI PASSE sur ce port 80.

**Position relative :** IDS est **derrière** le firewall, à l'intérieur. Il voit le trafic qui a déjà franchi le périmètre.

### 4.1 IDS vs IPS — Différence d'implémentation physique

| | IDS | IPS |
|-|-----|-----|
| Connexion physique | **Out-of-band** (tap/SPAN port) | **In-line** (dans le chemin) |
| Trafic passe par lui | ❌ Copie seulement | ✅ Tout le trafic |
| Action | Alerte (passive) | Bloque/modifie (active) |
| Si défaillance | Aucun impact réseau | Peut couper le réseau |
| Faux positif → | Alerte non pertinente | **Blocage de trafic légitime** |

**IPS est une extension de l'IDS** — fait tout ce que l'IDS fait + prévention. Pas un système distinct.

### 4.2 Les 3 méthodes de détection — comparaison mécanistique

#### Signature-Based (Misuse Detection)
```
Paquet → Pattern matching vs base de signatures
Match ? → Connexion coupée + drop + log + alarme
Pas de match → Passe à anomaly detection
```
- **Composant clé :** Interference Engine
- **Force :** Précision, peu de faux positifs
- **Faille :** Zero-day invisible, variantes d'attaques non détectées si signature trop stricte
- **Opérationnel :** Base de signatures doit être mise à jour en continu

#### Anomaly-Based Detection
```
Phase apprentissage → établit baseline (comportement normal)
En production → déviation > seuil → anomalie détectée
Anomalie → Connexion coupée + drop + log + alarme
Pas d'anomalie → Passe à stateful protocol analysis
```
- **Composant clé :** Anomaly Detection Engine
- **Force :** Détecte zero-day et attaques inconnues
- **Faille :** Taux de faux positifs élevé (comportement légitime inhabituel = alerte)
- **Techniques :** IA, réseaux de neurones, data mining, statistiques

#### Stateful Protocol Analysis
```
Profils RFC définis par fournisseur → comportement normal du protocole
Trafic observé vs profil RFC
Déviation (séquences imprévues, commandes anormales, longueurs suspectes) → alerte
```
- **Force :** Détecte violations de protocoles même inconnues (car basé sur RFC, pas signatures)
- **Faille :** Opérateurs doivent avoir connaissance approfondie des protocoles

**Différence anomaly vs protocol analysis :**
- Anomaly : déviation comportementale générale (volume, fréquence, patterns utilisateur)
- Protocol analysis : déviation par rapport aux spécifications RFC d'un protocole précis

### 4.3 Classification IDS — 6 axes

1. **Approche :** Signature-based vs Anomaly-based
2. **Système protégé :** NIDS (réseau) / HIDS (hôte) / Hybride
3. **Structure :** Centralisé vs Distribué
4. **Source données :** Journaux d'audit vs Paquets réseau
5. **Comportement post-attaque :** Actif (répond auto) vs Passif (alerte seulement)
6. **Temps d'analyse :** Temps réel (on-the-fly) vs Périodique (interval-based)

### 4.4 NIDS vs HIDS vs Hybride

| | NIDS | HIDS | Hybride |
|-|------|------|---------|
| Protège | Réseau | Hôte individuel | Les deux |
| Voit trafic chiffré | ❌ (chiffré au niveau réseau) | ✅ (déchiffré à l'hôte) | ✅ |
| Faux positifs | Moins | Plus | Optimisé |
| Détection zero-day | Moins (anomaly-based) | Mieux | Optimal |
| Installation | Points stratégiques réseau | Sur chaque hôte critique | Complexe |

**VPN + NIDS = aveugle :** Si le trafic est chiffré de bout en bout, le NIDS ne peut pas inspecter le payload → nécessite SSL/TLS interception ou HIDS.

### 4.5 Matrice des types d'alertes

| | Attaque réelle | Pas d'attaque |
|-|---------------|---------------|
| **Alerte déclenchée** | ✅ Vrai Positif | ⚠️ Faux Positif |
| **Pas d'alerte** | 🚨 **Faux Négatif** | ✅ Vrai Négatif |

**Faux négatif = défaillance fatale.** La mission d'un IDS est la détection — ne pas détecter une vraie attaque est un échec systémique.

**Faux positif = alert fatigue** → les admins deviennent insensibles → les vraies alertes sont manquées.

### 4.6 Déploiement NIDS — 4 positions optimales

| Position | Avantage principal |
|----------|--------------------|
| **Derrière FW externe (DMZ)** | Détecte attaques qui passent le FW, surveille trafic sortant serveurs compromis |
| **À l'extérieur du FW** | Quantifie le volume d'attaques Internet totales (forensics, tuning) |
| **Sur les dorsales réseau** | Volume maximum d'analyse, détection non-autorisés |
| **Sur sous-réseaux critiques** | Protection ciblée des assets les plus sensibles |

### 4.7 Console IDS — règle critique

**La console IDS doit être sur un système dédié.** Si installée sur le firewall ou un serveur de backup, elle ralentit significativement la réponse aux incidents. Logiciel de référence : **Sguil**.

### 4.8 Outils

| Outil | Type | Points clés |
|-------|------|-------------|
| **Snort** | NIDS open-source | Pattern matching, détecte buffer overflow, stealth port scans, OS fingerprinting, règles flexibles |
| **Suricata** | NIDS + IPS + NSM | In-line IPS, traitement PCAP offline |
| **OSSEC** | HIDS | Analyse de logs |
| **Zeek (Bro)** | NSM | Surveillance sécurité réseau |

---

## 5. CH5 — Honeypot

### Core Mental Model

Le honeypot **inverse la logique défensive** : au lieu de bloquer les attaquants, il les **attire délibérément** pour les observer. C'est un outil de **Threat Intelligence**, pas de protection directe.

**Conséquence logique :** Tout trafic vers un honeypot est suspect par définition → **taux de faux positifs quasi-nul** (vs IDS avec beaucoup de faux positifs).

### 5.1 Propriétés obligatoires d'un Honeypot

1. **Aucune activité autorisée** — tout trafic entrant est suspect
2. **Aucune valeur de production** — il ne sert à rien de légitime
3. **Volontairement vulnérable** — conçu pour être compromis
4. **Instrumenté** — chaque action est enregistrée

### 5.2 Classification par niveau d'interaction

| Niveau | Ce qui est simulé | Risque | Données collectées | Exemple |
|--------|-------------------|--------|-------------------|---------|
| **Faible** | Services limités | Faible | Scans, tentatives basiques | Détection rapide |
| **Moyen** | OS réel + apps | Moyen | Comportement post-connexion | HoneyBOT |
| **Fort** | Tous services d'un réseau cible | Élevé | TTPs complets | Recherche avancée |
| **Pur** | Émule réseau de production réel | Très élevé | Maximaux | Émulation complète |

**Gradient :** Faible interaction → moins de données, moins de risque. Forte interaction → données riches, risque de pivot.

### 5.3 Honeypot de Production vs Recherche

| | Production | Recherche |
|-|-----------|----------|
| Déployé **dans** le réseau de production | ✅ | ❌ (environnement isolé) |
| Niveau d'interaction | Faible à moyen | **Fort** |
| Objectif | Détecter menaces opérationnelles | Comprendre les adversaires (TTPs) |
| Détecte menaces internes | ✅ (position LAN) | Non (environnement isolé) |
| Déployé par | Entreprises | Instituts, gouvernements, militaires |

**Pourquoi le honeypot de production détecte les menaces internes :** Il est positionné sur le LAN interne. Aucun utilisateur légitime n'a de raison de l'accéder (aucune valeur de production). Tout accès depuis l'intérieur déclenche une alerte.

### 5.4 Types spécialisés de Honeypots

| Type | Vecteur ciblé | Mécanisme |
|------|--------------|-----------|
| **Malware Honeypot** | Campagnes malware | Système vulnérable dans l'infra réseau |
| **Database Honeypot** | SQLi, énumération BDD | Fausses BDD vulnérables |
| **Spam Honeypot** | Spammeurs | Serveurs SMTP ouverts (open relay) + proxies ouverts |
| **Email Honeypot** | Phishing, harvesting | Fausses adresses email |
| **Spider Honeypot** | Crawlers malveillants | Pages web avec liens piégés (invisibles aux humains) |
| **Honeynet** | Profil adversarial complet | Réseau de plusieurs honeypots interconnectés |

**Honeynet = le plus puissant.** Observe mouvement latéral, escalade de privilèges, exfiltration — l'attaquant croit être dans un vrai réseau.

### 5.5 Positionnement architectural

```
[Réseau Interne] → [Firewall] → [DMZ]
                                 ├── Serveur Web (production)
                                 └── Honeypot (leurre)
                             → [Packet Filter] → [Internet]
```

**Risque critique :** Un honeypot mal isolé peut devenir un **pivot** vers le réseau interne. Isolation stricte du LAN obligatoire.

**Comportement inverse du Firewall :** Le packet filter côté Internet **laisse délibérément passer** le trafic vers le honeypot — comportement intentionnellement opposé à un firewall classique.

### 5.6 Comparaison Honeypot vs IDS vs Firewall

| Critère | Honeypot | IDS | Firewall |
|---------|---------|-----|---------|
| Rôle | Attirer + observer | Détecter | Bloquer |
| Protège directement | ❌ | ❌ | ✅ |
| Faux positifs | ≈ 0 | Élevé (anomaly) | N/A |
| TTPs collectés | ✅ Maximum | Partiel | ❌ |
| Détecte menaces internes | ✅ (production) | Partiel | ❌ |

---

## 6. CH6 — Proxy Server

### Core Mental Model

Le proxy adresse l'**exposition directe des ressources internes**. Sans proxy, un client interne communique directement avec l'externe — exposant son IP, son OS, sa pile TCP. Avec proxy : double session TCP, l'interne est invisible.

**Double terminaison TCP :** client → proxy (session 1) + proxy → serveur (session 2). Pas de connexion directe client-serveur.

### 6.1 Proxy vs NAT vs Firewall — triangle des confusions

| | Proxy | NAT | Firewall Stateless |
|-|-------|-----|-------------------|
| Couche OSI | **L7** | **L3** | L3-L4 |
| Comprend les protocoles applicatifs | ✅ | ❌ | ❌ |
| Inspecte le payload | ✅ | ❌ | ❌ |
| Double session TCP | ✅ | ❌ | ❌ |
| En cas de panne | **Fail-closed** (tout s'arrête) | Fail-open (trafic passe) | Dépend config |
| Journalisation | Full payload (URL, User-Agent, contenu) | Headers seulement | Headers seulement |

### 6.2 Types de Proxy — mécanismes précis

#### Proxy Transparent
- Intercepte **sans configuration client**
- Redirection via règles iptables/NAT au niveau routeur/FW
- En-tête `X-Forwarded-For` peut trahir sa présence
- Usage : FAI, entreprises avec masse de postes, captive portals
- **Limite SSL :** HTTPS nécessite SSL bumping (interception TLS) → problèmes de confiance certificats

#### Proxy Non-Transparent (Explicite)
- Configuration client requise par programme (ou PAC file via GPO)
- Transmet l'URL complète avec FQDN → décisions de filtrage granulaires
- **4 services additionnels :**
  1. Group Annotation Services (catégorisation par groupe)
  2. Media Type Transformation (conversion formats à la volée)
  3. Protocol Reduction (filtrage protocoles complexes)
  4. Anonymity Filtering (suppression headers d'identité)
- **Risque bypass :** si une appli ne supporte pas la config proxy, elle contourne → vecteur d'exfiltration

#### Proxy SOCKS (couche 5 — Session)
- **Agnostique au protocole applicatif** — tunnel TCP/UDP générique
- Peut transporter n'importe quel protocole (HTTP, FTP, SMTP, SSH...)
- **Pas de cache HTTP** (contrairement au proxy HTTP)
- SOCKS5 (RFC 1928) : TCP + UDP + authentification (user/pass ou GSS-API) + résolution DNS côté proxy
- 3 composants : serveur SOCKS + programme client + bibliothèque cliente SOCKS
- **Usage offensif :** chaînage de proxies dans les botnets C2 / obfuscation d'origine (Tor est basé sur SOCKS)

#### Proxy Anonyme
- Supprime/modifie les headers révélateurs (`X-Forwarded-For`, `Via`, `X-Real-IP`)
- 3 niveaux : Elite (invisible), Anonymous (révèle l'usage d'un proxy), Transparent (révèle l'IP réelle)
- Le proxy lui-même connaît l'IP réelle → confiance totale requise envers le fournisseur

#### Reverse Proxy
- Positionné **côté serveur** (pas côté client)
- Le client croit parler directement au serveur web — le serveur backend est invisible
- Fonctions : load balancing, SSL termination, caching, compression, protection DDoS, WAF
- Exemples : Nginx, HAProxy, Cloudflare

| Critère | Forward Proxy | Reverse Proxy |
|---------|--------------|---------------|
| Position | Côté client | Côté serveur |
| Protège | Client (cache IPs internes) | Serveur (cache infra backend) |
| Client conscient | Parfois (non-transparent : oui) | **Jamais** |
| Exemples | Squid, CCProxy | Nginx, HAProxy |

### 6.3 Limitations critiques du Proxy

1. **SPOF (Single Point of Failure)** : panne = toutes communications stoppées (fail-closed)
2. **Configuration par service** : chaque service doit être configuré individuellement
3. **Latence additionnelle** : reroutage, inspection, reconstruction des paquets
4. **Open proxy** : si mal sécurisé, devient exploitable par des tiers pour anonymiser des attaques
5. **Chargement partiel** : le filtrage peut bloquer des éléments légitimes de page

---

## 7. CH7 — VPN

### Core Mental Model

Le VPN crée un canal logiquement privé sur Internet. Il garantit **confidentialité + intégrité + authentification** — mais pas l'anonymat complet ni le chemin de transmission.

**3 mécanismes combinés :** Encapsulation (masque topologie) + Chiffrement (protège contenu) + Authentification (vérifie identité).

### 7.1 Encapsulation vs Chiffrement — deux mécanismes distincts

**Encapsulation :** enferme le paquet IP original dans un nouveau paquet avec les IPs des équipements VPN. Masque les adresses réelles et la topologie interne.

**Chiffrement :** rend le contenu illisible pour un observateur externe.

**L2TP seul ne chiffre PAS — c'est L2TP/IPsec qui chiffre.** L2TP = tunneling seulement.

### 7.2 Protocoles VPN — couches et propriétés

| Protocole | Couche | Chiffre | Note |
|-----------|--------|---------|------|
| PPTP | L2 | Oui (MS-CHAPv2) | **Vulnérable** (MS-CHAPv2 cassé) |
| L2TP | L2 | ❌ seul | Couplé avec IPsec pour le chiffrement |
| **IPsec** | **L3** | ✅ | Standard industriel, site-à-site + distant |
| SSL/TLS | L4-L7 | ✅ | Accès web, clientless |
| OpenVPN | L4-L7 | ✅ (OpenSSL) | Flexible, port 1194 |
| SSH | App | ✅ | Tunneling applicatif |

### 7.3 IPsec vs SSL — décision architecturale

| | IPsec | SSL |
|-|-------|-----|
| Client requis | Logiciel dédié | Navigateur suffit (clientless) |
| Accès depuis poste non géré | ❌ Difficile | ✅ |
| Usage principal | Site-à-site, accès distant corporatif | Accès distant granulaire |
| Couche | L3 | L4-L7 |
| Complexité config | Plus élevée | Plus simple |

**3 applications IPsec :**
1. VPN accès distant (télétravailleurs)
2. VPN intranet (liaison siège-agences)
3. VPN extranet (connectivité inter-organisations partenaires)

### 7.4 Trusted vs Secure vs Hybrid VPN

| | Trusted VPN | Secure VPN | Hybrid VPN |
|-|-------------|-----------|-----------|
| Chiffrement données | ❌ | ✅ | ✅ |
| Intégrité chemin | ✅ (opérateur) | ❌ | ✅ |
| Technologies | ATM, Frame Relay, MPLS | IPsec, SSL, OpenVPN | Secure sur Trusted |
| Garantit | Chemin privé | Données chiffrées | Les deux |

**⚠️ Trusted ≠ sécurisé par chiffrement.** Il est "de confiance" parce que l'opérateur garantit le chemin, pas la confidentialité.

### 7.5 Topologies VPN

| Topologie | Communication directe entre branches | Panne centrale | Usage |
|-----------|--------------------------------------|----------------|-------|
| Hub-and-Spoke | ❌ (via hub) | Tout tombe | Multi-sites entreprise |
| Point-to-Point | ✅ | N/A | 2 datacenters |
| Full Mesh | ✅ | Non (redondant) | Réseaux critiques |
| Star | ❌ (strictement interdit) | Tout tombe | **Banques, finance** |

**Star vs Hub-and-Spoke :** La distinction est que dans la Star, la communication entre branches est **architecturalement interdite** (pas juste routée via hub). Tout attaquant doit d'abord compromettre le siège.

### 7.6 Concentrateur VPN — 8 fonctions

1. Chiffrement/déchiffrement
2. Authentification des utilisateurs
3. Gestion du transfert de données dans le tunnel
4. Négociation des paramètres du tunnel
5. Gestion des clés de sécurité
6. Établissement des tunnels
7. Assignation des adresses utilisateur
8. Gestion des flux entrants/sortants

---

## 8. CH9 — Load Balancer

### Core Mental Model

Le Load Balancer est à la fois un outil de **performance** (distribution) ET de **sécurité** (absorption DoS/DDoS). Il est positionné entre les deux firewalls dans l'architecture standard.

```
Internet → [FW Externe] → [Load Balancer] → [FW Interne] → Intranet
                                ↕
                              DMZ
```

### 8.1 Algorithmes d'équilibrage — mécanismes précis

| Algorithme | Dynamique | Critère | Cas optimal |
|------------|-----------|---------|-------------|
| **Round-Robin** | ❌ Statique | Séquentiel cyclique | Serveurs homogènes, charge uniforme |
| **Weighted Round-Robin** | ❌ Semi-statique | Poids admin (manuel) | Serveurs hétérogènes |
| **Least Connections** | ✅ | Nb connexions actives | Sessions longues variables |
| **Random Connections** | ✅ | Aléatoire entre 2 + Least Conn | Grand volume, serveurs similaires |
| **Least Time** | ✅ | Temps de réponse | Apps sensibles à la latence |
| **Hash** | Déterministe | Clé prédéfinie (IP, URL) | Persistance, cache |
| **Source IP** | Déterministe | IP source client | Persistance par utilisateur |

**Weighted Round-Robin :** le poids est défini **manuellement par l'administrateur**, pas calculé automatiquement.

**Random Connections ≠ purement aléatoire :** sélectionne 2 serveurs aléatoirement, puis applique Least Connections entre ces 2.

### 8.2 Session Affinity — mécanisme détaillé

```
1. Première requête → LB sélectionne serveur S1
2. LB crée en cache mémoire : cookie_session_X → S1
3. Serveur S1 génère cookie de session (dans réponse HTTP)
4. Client inclut ce cookie dans toutes les requêtes suivantes
5. LB lit le cookie dans l'en-tête → identifie S1 → redirige vers S1
6. Session reste sticky pendant toute sa durée
```

**Nécessite un LB L7** (doit lire les cookies HTTP). Un LB L4 ne voit pas les cookies.

**Trade-off :** Session Affinity peut créer un déséquilibre — un serveur avec des sessions longues est surchargé pendant que les autres sont libres.

**Contextes qui l'exigent :** e-commerce (panier), jeux en ligne, workflows multi-étapes, toute app stateful stockant l'état localement.

### 8.3 Clustering LB — éviter le SPOF

```
VIP (Virtual IP exposée aux clients : 192.0.2.1)
        │
   [Active LB] ← Traite le trafic en temps réel
   [Passive LB] ← En veille, prend le VIP si Active tombe
        │
   [Web Cluster]
   ├── Serveur 1 (192.168.0.1:80)
   ├── Serveur 2 (192.168.0.2:80)
   └── Serveur 3 (192.168.0.3:80)
```

**4 bénéfices du clustering :** Haute disponibilité + Répartition de charge + Tolérance aux pannes + Scalabilité.

### 8.4 L4 vs L7 Load Balancing

| | L4 LB | L7 LB |
|-|-------|-------|
| Données inspectées | IP + Port | Headers HTTP, cookies, URL, contenu |
| Session Affinity | ❌ | ✅ |
| Content-based routing | ❌ | ✅ (ex: /api → serveur API) |
| Performance | Plus rapide | Plus lente (inspection applicative) |

---

## 9. Carte d'intégration cross-chapitres

### Flèches de dépendance conceptuelle

```
Ch2 Segmentation
    ├── Fournit les zones (DMZ, VLAN, ZT) utilisées par Ch3, Ch4, Ch5
    └── Les ACLs Ch3 sont le mécanisme d'application des politiques VLAN Ch2

Ch3 Firewall
    ├── Application Proxy (Ch3) ≈ Forward Proxy (Ch6) — même concept, couche 7
    ├── NAT Ch3 + Proxy Ch6 font tous les deux de la translation d'adresse (niveaux différents)
    └── DMZ single/dual FW (Ch2) nécessite les technologies FW de Ch3

Ch4 IDS/IPS
    ├── L'IDS complète le FW — FW filtre L3-L4, IDS inspecte contenu
    ├── NIDS aveugle sur trafic VPN chiffré (Ch7) → HIDS compense
    └── Alertes IDS alimentent les signatures → améliore les règles FW

Ch5 Honeypot
    ├── Positionné en DMZ (Ch2) avec isolation stricte
    ├── Spam honeypot cible les open proxies (Ch6)
    └── Données honeypot → IOCs → règles IDS/FW : cycle de renseignement

Ch6 Proxy
    ├── Reverse Proxy ≈ Load Balancer L7 (Ch9) — distinctions subtiles
    ├── SOCKS5 utilisé dans tunneling VPN-like (Ch7)
    └── Proxy non-transparent + authentification → lien AAA

Ch7 VPN
    ├── Trafic VPN chiffré = NIDS aveugle (Ch4) → SSL inspection nécessaire
    ├── VPN Hardware intègre load balancing natif (Ch9)
    └── Architecture : External FW → VPN Concentrateur (Ch3 + Ch7)

Ch9 Load Balancer
    ├── Positionné entre les deux firewalls (Ch3)
    ├── Reverse Proxy (Ch6) peut jouer rôle de LB L7
    └── Session Affinity (Ch9) + IPS inline (Ch4) : complexité — différentes requêtes d'une session peuvent passer par différents IPS
```

### Interconnexions clés à mémoriser pour l'examen

1. **ACLs (Ch3) + VLANs (Ch2) :** Les VLANs isolent au L2. Les ACLs appliquent les règles inter-VLAN au L3/L4. Les deux sont nécessaires.

2. **Proxy (Ch6) + FW (Ch3) :** FW filtre au périmètre (L3-L4), Proxy inspecte le contenu applicatif (L7). Complémentaires, pas substituables.

3. **IDS (Ch4) + Honeypot (Ch5) :** L'IDS génère beaucoup de faux positifs. Le Honeypot génère quasi-zéro faux positifs. Le cycle : honeypot → TTPs → règles IDS/FW.

4. **VPN (Ch7) + IDS (Ch4) :** Le VPN chiffre → l'IDS réseau est aveugle. Solution : SSL inspection sur le FW/Proxy ou HIDS sur les hôtes.

5. **Load Balancer (Ch9) + Proxy Inverse (Ch6) :** Un reverse proxy (Nginx) peut servir de LB L7. Un LB pur (HAProxy L4) ne peut pas faire de content-based routing. La distinction est sur les capacités applicatives.

---

## 10. Distinctions Expert — Les pièges qui font la différence

### D1 : Zero Trust — "protège les accès" pas "le réseau"
La formulation exacte du cours est critique. Le ZT **supprime la confiance implicite liée à la position réseau** (intérieur = fiable). Chaque accès est vérifié indépendamment.

### D2 : IDS Actif ≈ IPS mais pas identique
IDS Actif = répond automatiquement sans intervention humaine. IPS = in-line, bloque en temps réel. Un IDS actif peut être out-of-band et envoyer des TCP resets, ce qui est différent d'un vrai blocage in-line.

### D3 : Honeypot Faible vs Moyen vs Fort vs Pur
- **Faible** : services limités (pas d'OS complet)
- **Moyen** : OS réel (HoneyBOT)
- **Fort** : tous services d'un réseau cible
- **Pur** : émule le **vrai** réseau de production — pas une simulation, une émulation fidèle

### D4 : Proxy Transparent — qui configure quoi
Le proxy **transparent** ne requiert **aucune configuration client**. Le proxy **non-transparent** requiert une configuration par programme. L'inverse est la confusion classique.

### D5 : L2TP vs L2TP/IPsec
L2TP seul = tunneling sans chiffrement. L2TP/IPsec = tunneling + chiffrement. Confondre les deux = réponse fausse garantie.

### D6 : Trusted VPN — "de confiance" ≠ "sécurisé"
Trusted = l'opérateur garantit le chemin (circuits dédiés). Les données ne sont **pas chiffrées**. Un attaquant sur l'infrastructure de l'opérateur peut lire les données.

### D7 : Round-Robin vs Weighted Round-Robin
Round-Robin : ignorant de la capacité des serveurs. Weighted : l'administrateur assigne manuellement les poids — c'est une configuration statique, pas une adaptation dynamique.

### D8 : Session Affinity vs Source IP
- Session Affinity : utilise les **cookies HTTP** (L7)
- Source IP : utilise l'**adresse IP source** (L4)
Les deux maintiennent la persistance mais par mécanismes différents et à des niveaux différents.

### D9 : NAT Destination vs Reverse Proxy
NAT Destination (port forwarding) opère L3 — traduit les adresses, ne comprend pas HTTP. Un Reverse Proxy opère L7 — comprend HTTP, peut inspecter, modifier, mettre en cache.

### D10 : Star vs Hub-and-Spoke
Star : communication inter-branches **architecturalement interdite**. Hub-and-Spoke : communication inter-branches impossible uniquement parce que tout passe par le hub (pourrait être configuré autrement). La Star est plus restrictive par design.

### D11 : Bastion Host vs Firewall
Le bastion **n'est pas un firewall**. Il est un système durci qui **fonctionne avec** le firewall. Le firewall filtre ; le bastion est le point d'accès unique sécurisé. Un bastion placé sur le réseau interne est explicitement déconseillé.

### D12 : Segmentation physique ≠ la plus utilisée
La segmentation physique est "la plus sécurisée" en isolation. Mais son coût et sa complexité la rendent **rarement utilisée en pratique**. Les VLANs dominent.

---

## 11. Analyse des surfaces d'attaque & failure modes

### Par technologie : quand ça échoue et comment

#### Firewall — failure modes
- **Misconfiguration** : règle trop permissive, ordre incorrect (règle spécifique après règle générale)
- **Trafic tunnelisé** : payload malveillant dans HTTPS/DNS — le FW voit un protocole autorisé
- **IP Spoofing** : contre le stateless packet filtering
- **Application layer attacks** : SQLI dans une requête HTTP autorisée — FW ne voit que le port 80
- **Coordination interne** : backdoor — le FW ne voit pas le trafic initié de l'intérieur vers une destination externe légitime

#### IDS — failure modes
- **Faux négatif** : attaque non signée + comportement dans la baseline = invisible
- **Chiffrement** : NIDS aveugle sur trafic TLS/VPN
- **Évasion par fragmentation** : paquet fragmenté pour éviter le pattern matching
- **Polymorphic malware** : signature change à chaque exécution
- **Slow scan / Low and slow** : comportement sous le seuil d'anomalie

#### Honeypot — failure modes
- **Pivot** : attaquant utilise le honeypot compromis comme tremplin vers le LAN
- **Fingerprinting** : un attaquant sophistiqué peut détecter les artefacts du honeypot (comportements non-réalistes, timestamps, services simulés) et éviter de s'y engager
- **Légal** : dans certaines juridictions, un honeypot peut être considéré comme une "entrapment" (piège illégal) si mal configuré

#### VPN — failure modes
- **Split tunneling** : si activé, le trafic non-entreprise passe en clair — vecteur d'attaque
- **Certificats invalides** : MITM possible si validation des certificats désactivée
- **L2TP sans IPsec** : tunnel non chiffré
- **VPN concentrateur compromis** : point de défaillance critique — gère tous les tunnels

#### Proxy — failure modes
- **Open proxy** : proxy non sécurisé exploitable par des tiers
- **SSL Bumping** : intercepte HTTPS → viole la confiance TLS → doit être accompagné de la distribution du certificat proxy aux clients
- **Proxy bypass** : applications qui ne supportent pas la config proxy contournent
- **SPOF** : fail-closed = tout s'arrête si proxy tombe

#### Load Balancer — failure modes
- **Session Affinity + node failure** : si le serveur d'une session sticky tombe, la session est perdue
- **LB lui-même = SPOF** → solution : clustering Active/Passive + VIP
- **Déséquilibre avec Session Affinity** : sessions longues créent une concentration sur certains nœuds

### Vecteurs d'attaque cross-chapitres

**Attaque de mouvement latéral complète :**
```
1. Phishing → compromission poste utilisateur (contourne FW — trafic initié par l'intérieur)
2. Aucune détection IDS (trafic légitime HTTPS vers C2)
3. Pivot via proxy ouvert ou tunnel DNS
4. Mouvement latéral est-ouest (non inspecté si pas de micro-segmentation)
5. Compromission de données sensibles
```

**Mitigation par couche :**
- FW : bloque connexions externes initiées + filtre DNS exfiltration
- IDS : anomaly-based détecte le comportement inhabituels du poste compromis
- Honeypot : si l'attaquant sonde le réseau interne, atteint le honeypot
- Zero Trust : chaque connexion interne est vérifiée → bloque le mouvement latéral
- Micro-segmentation : limite le rayon d'explosion

---

## 12. 25 Questions d'Examen de Niveau Avancé

---

**Q1.** Un administrateur configure un réseau en séparant les serveurs Web, Email et DNS dans des segments différents connectés via un switch unique avec des VLANs. Un attaquant envoie des trames avec double-tagging. Quel mécanisme d'attaque exploite-t-il et quelle est la mitigation correcte ?

**Réponse :** VLAN hopping via double-tagging. L'attaquant encapsule une trame avec deux tags VLAN : le tag externe correspond au VLAN natif du trunk, le tag interne correspond au VLAN cible. Le switch retire le premier tag et transmet la trame avec le second tag vers le VLAN cible. Mitigation : (1) changer le VLAN natif sur tous les ports trunk vers un VLAN inutilisé, (2) désactiver DTP (Dynamic Trunking Protocol), (3) ne jamais assigner d'hôtes au VLAN natif.

---

**Q2.** Expliquez pourquoi un pare-feu à inspection d'état (stateful) est fondamentalement supérieur à un filtre de paquets stateless pour détecter une attaque de session hijacking.

**Réponse :** Un filtre stateless évalue chaque paquet indépendamment — il ne sait pas si un paquet appartient à une session existante. Un attaquant peut injecter un paquet avec des IPs/ports valides dans une session établie sans que le FW le détecte. L'inspection stateful maintient une table d'état avec les paramètres de chaque session active (IP src/dst, ports, numéros de séquence TCP). Tout paquet ne correspondant pas à une session dans la table est bloqué au niveau TCP. L'injection mid-session produit un numéro de séquence incohérent ou provient d'une connexion non enregistrée → bloqué.

---

**Q3.** Pourquoi un NIDS est-il inefficace face au trafic VPN chiffré, et quelle architecture permet de résoudre ce problème ?

**Réponse :** Le NIDS analyse le payload des paquets pour détecter des signatures ou anomalies. Le trafic VPN chiffré est opaque — le NIDS voit des données chiffrées sans pouvoir les interpréter. Deux solutions : (1) Configurer une SSL inspection (TLS interception) sur le proxy ou NGFW — le VPN est terminé, le trafic inspecté, puis re-chiffré. (2) Déployer un HIDS sur les hôtes — il analyse le trafic après déchiffrement par le client VPN. L'IDS hybride (NIDS + HIDS) est l'approche optimale.

---

**Q4.** Distinguez la détection d'anomalies de l'analyse d'état de protocole dans un IDS. Dans quel cas l'analyse de protocole peut-elle détecter une attaque zero-day ?

**Réponse :** La détection d'anomalies établit une baseline comportementale (volumes, fréquences) et signale les déviations. L'analyse de protocole utilise des profils définis par les fournisseurs basés sur les RFC pour chaque protocole et détecte les déviations par rapport aux spécifications. L'analyse de protocole peut détecter des zero-day si l'attaque viole les RFC — par exemple une commande SMTP non définie dans RFC 5321, ou un malware qui crée des séquences TCP impossibles selon RFC 793. Si l'attaque respecte scrupuleusement les RFC mais exploite une logique applicative, l'analyse de protocole sera aveugle.

---

**Q5.** Un Honeypot est positionné dans la DMZ. Un attaquant le compromet. Quel est le risque principal et comment l'architecte réseau doit-il le mitiger ?

**Réponse :** Risque principal : le honeypot compromis devient un pivot (tremplin) vers le réseau interne. L'attaquant peut lancer des attaques depuis le honeypot vers le LAN, en bénéficiant d'une position déjà à l'intérieur du périmètre. Mitigation : isolation stricte du honeypot du LAN (règles FW interne bloquant tout trafic honeypot → LAN), limitation des connexions sortantes depuis le honeypot, monitoring exhaustif des connexions sortantes, et containment automatique (kill switch) si comportement de pivot détecté.

---

**Q6.** Comparez le comportement en cas de panne d'un serveur proxy (fail-closed) et d'un filtre de paquets (fail-open). Quelles sont les implications sécuritaires ?

**Réponse :** Un proxy adopte un comportement fail-closed : toute panne interrompt toutes les communications réseau passant par lui. Bien que cela impacte la disponibilité, c'est le comportement le plus sécurisant — aucun trafic non inspecté ne peut passer. Un filtre de paquets dans de nombreuses configurations adopte un comportement fail-open : en cas de panne, les paquets traversent sans filtrage. Cela préserve la disponibilité mais expose le réseau à des attaques non filtrées. La priorité (disponibilité vs sécurité) détermine la configuration. Dans les environnements haute sécurité, les FW sont configurés en fail-closed aussi.

---

**Q7.** Expliquez pourquoi la topologie VPN en étoile (Star) est utilisée dans les réseaux bancaires. Quelle propriété de sécurité supplémentaire offre-t-elle par rapport au Hub-and-Spoke classique ?

**Réponse :** Dans la topologie Star, la communication entre branches est architecturalement interdite par design — non juste routée via le hub, mais explicitement bloquée. Dans un Hub-and-Spoke, la communication inter-branches passe par le hub mais pourrait être reconfigurée. La topologie Star garantit qu'un attaquant compromettant une agence bancaire ne peut pas atteindre les autres agences directement — il doit d'abord compromettre le siège central. Cela contient les brèches et limite le rayon d'explosion (blast radius).

---

**Q8.** Un administrateur déploie un IDS avec détection par signatures uniquement. Un malware polymorphique modifie sa signature à chaque exécution. Quelle approche de détection devrait être ajoutée et pourquoi est-elle adaptée à ce cas ?

**Réponse :** La détection par anomalies. Un malware polymorphique modifie son code (et donc sa signature) mais conserve généralement un comportement observable : connexions sortantes vers des IPs non habituelles, consommation CPU anormale, création de processus inhabituels, communication avec des ranges IP inhabituels. La détection par anomalies, basée sur la déviation par rapport à la baseline comportementale, peut détecter ces patterns indépendamment de la signature. L'approche hybride (signatures pour efficacité sur attaques connues + anomalies pour les inconnues) est la solution optimale.

---

**Q9.** Distinguez les 4 types de NAT et expliquez pourquoi NAT ne peut pas être considéré comme un firewall complet.

**Réponse :** (1) Static 1:1 : mappage fixe IP interne ↔ IP externe. (2) Dynamic address : IP externe assignée dynamiquement sans modification de port. (3) NAPT/PAT : plusieurs internes partagent une IP externe via la translation de ports — le plus courant. (4) Destination NAT : IP du routeur redirigée vers serveur interne.

NAT n'est pas un firewall complet car : (1) il n'inspecte pas le contenu des paquets, (2) il ne maintient pas de politique de sécurité explicite, (3) il ne filtre pas les types de trafic au-delà de la translation d'adresse, (4) un tunnel peut traverser NAT si initié depuis l'intérieur (split-tunneling, reverse shells). NAT fonctionne **comme** un firewall pour les connexions entrantes non-initiées, mais c'est un effet secondaire, pas sa fonction principale.

---

**Q10.** Expliquez le mécanisme de Session Affinity dans un Load Balancer L7. Dans quel scénario peut-elle devenir un vecteur de DoS ?

**Réponse :** La Session Affinity utilise les cookies HTTP pour lier une session à un serveur spécifique. Le LB inspecte les cookies entrants, consulte son cache mémoire pour identifier le serveur associé, et redirige systématiquement vers ce serveur.

Vecteur DoS : un attaquant peut maintenir artificiellement des sessions longues (slowloris style) avec un serveur spécifique. Grâce à la Session Affinity, toutes ses connexions sont dirigées vers le même serveur, l'épuisant progressivement pendant que les autres serveurs restent libres. Mitigation : timeouts de session agressifs, limite de connexions par IP, combiné avec le protocole de load balancing sous-jacent qui ignore l'affinité pour les nouvelles connexions si un serveur dépasse un seuil.

---

**Q11.** Quelle est la différence fondamentale entre un Proxy SOCKS et un Proxy HTTP en termes de capacité d'inspection ? Quelles implications pour la sécurité réseau ?

**Réponse :** Un proxy HTTP opère à la couche application (L7) et comprend le protocole HTTP — il peut inspecter les URLs, méthodes (GET/POST), headers, et filtrer les contenus. Il dispose de capacités de cache et d'authentification applicative.

SOCKS opère à la couche session (L5) et crée un tunnel générique TCP/UDP sans interpréter le contenu. Il est agnostique au protocole applicatif. Implication sécurité : un trafic passant via SOCKS est opaque à tous les systèmes d'inspection qui opèrent au-dessus de L5. C'est pourquoi SOCKS est massivement utilisé dans les botnets et infrastructures C2 — il permet de tunneliser n'importe quel protocole (y compris des protocoles de commande personnalisés) à travers un proxy réseau, contournant les inspections applicatives.

---

**Q12.** Décrivez le cycle complet de Threat Intelligence que génère un Honeynet et expliquez comment il alimente les autres couches de défense.

**Réponse :** (1) L'attaquant interagit avec le Honeynet, croyant évoluer dans un vrai réseau. (2) Ses TTPs (Tactics, Techniques, Procedures) sont entièrement capturés : vecteur d'entrée, exploits utilisés, outils déployés, commandes exécutées, C2 contactés, techniques de persistence, données exfiltrées. (3) Ces données génèrent des IOCs (Indicators of Compromise) : hashs de malwares, IPs C2, patterns comportementaux, signatures réseau. (4) Les IOCs alimentent les bases de signatures IDS → nouvelles règles de détection. (5) Les IPs C2 sont ajoutées aux blocklists FW. (6) Les techniques de persistence génèrent de nouvelles règles HIDS. (7) Les patterns comportementaux affinent les baselines de détection d'anomalies. Le cycle : observation → compréhension → règles → protection.

---

**Q13.** Pourquoi Zero Trust est-il décrit comme protégeant "les accès" et non "le réseau" ? Quelles implications architecturales cette distinction a-t-elle ?

**Réponse :** La sécurité périmétrique protège le réseau en créant une frontière interne/externe — une fois à l'intérieur, le trafic est implicitement de confiance. Zero Trust élimine cette distinction : la "position" dans le réseau (interne ou externe) ne confère aucune confiance. Chaque demande d'accès est vérifiée individuellement selon des critères : identité, rôle, état de l'appareil, heure, comportement.

Implications architecturales : (1) Le Control Plane vérifie chaque demande d'accès indépendamment de l'origine. (2) Le Data Plane est configuré pour n'accepter le trafic que du client spécifiquement autorisé. (3) La micro-segmentation permet d'appliquer des politiques granulaires à chaque ressource. (4) MFA + PAM + chiffrement sont obligatoires. Résultat : même un attaquant inside le réseau avec un compte légitime compromis doit contourner le Control Plane pour chaque ressource cible.

---

**Q14.** Un réseau a un IDS distribué et un IDS centralisé. Lequel est plus résistant à une attaque DDoS distribuée et pourquoi ?

**Réponse :** L'IDS distribué. Une attaque DDoS provient de milliers d'IP différentes, chaque nœud générant peu de trafic. Un IDS centralisé voit l'agrégat de tout le trafic mais peut être saturé par le volume total. Plus important, il constitue un SPOF — si l'attaquant comprend l'architecture, il peut cibler la console centrale. L'IDS distribué a des capteurs locaux qui analysent localement et partagent les alertes entre eux. Aucun point unique de défaillance. Chaque capteur voit son segment et peut détecter des patterns locaux, puis la corrélation distribuée permet la détection globale.

---

**Q15.** Comparez les bastion hosts single-interface et multi-interface. Dans quel cas le dual-interface sans routage est-il critique ?

**Réponse :** Single-interface : une seule NIC, tout le trafic (public et privé) passe par la même interface. Moins de séparation — le trafic interne et externe n'est pas physiquement distinct.

Multi-interface : ≥2 NICs, interface publique séparée de l'interface privée. Meilleure séparation physique des flux.

Dual-interface sans routage : la propriété critique est que le **routage est désactivé** entre les deux interfaces. Le bastion agit comme un vrai firewall — les paquets ne peuvent pas être routés d'une interface à l'autre automatiquement. Tout trafic doit être explicitement traité et relayé par l'application proxy sur le bastion. Si le routage est activé, un attaquant peut bypasser le proxy et router directement des paquets de l'interface publique vers le réseau interne via l'interface privée.

---

**Q16.** Expliquez pourquoi la détection d'anomalies génère davantage de faux positifs que la détection par signatures. Comment les IDS commerciaux adressent-ils ce problème ?

**Réponse :** La détection par signatures compare contre des patterns précis et prédéfinis — si la correspondance est exacte, c'est une attaque. Faux positifs quasi-nuls sur attaques connues car les règles sont spécifiques.

La détection d'anomalies suppose que tout comportement déviant de la baseline est suspect. Mais le trafic réseau est intrinsèquement imprévisible : un burst de trafic légitime lors d'une mise à jour, un nouveau service installé, un emploi du temps modifié — tout cela génère des anomalies sans qu'il y ait attaque. Les IDS commerciaux adressent cela par : (1) apprentissage adaptatif continu (la baseline se met à jour graduellement), (2) seuils configurables par type d'anomalie, (3) corrélation multi-sources (anomalie isolée vs anomalie corrélée avec d'autres indicateurs), (4) suppression des alertes redondantes, (5) scores de risque pondérés plutôt que binaires.

---

**Q17.** Dans quel scénario un Trusted VPN est-il plus approprié qu'un Secure VPN ? Justifiez avec les propriétés de chaque.

**Réponse :** Un Trusted VPN (MPLS, Frame Relay) est plus approprié quand : (1) L'organisation a besoin de **garanties de performance** (QoS, latence garantie, bande passante dédiée) — impossible avec Internet public. (2) L'organisation fait confiance à son opérateur et ne transmet pas de données hautement sensibles. (3) Les contraintes de conformité exigent un chemin réseau auditable et contrôlé. (4) La fiabilité du chemin est prioritaire sur la confidentialité des données.

Un Secure VPN est plus approprié quand la confidentialité des données est primordiale et quand les coûts des circuits dédiés sont prohibitifs. Le compromis ultime est le Hybrid VPN : circuit Trusted pour la garantie de chemin + couche Secure pour le chiffrement des données.

---

**Q18.** Décrivez les 5 phases de déploiement d'un firewall et identifiez laquelle est la plus souvent négligée en production.

**Réponse :** (1) Planification : évaluation des risques, identification des menaces, architecture, politique de sécurité. (2) Configuration : paramétrage hardware/software, politiques, journalisation, alertes. (3) Tests : vérification des règles contre les comportements attendus, détection de bugs. (4) Déploiement : mise en production conforme aux politiques organisationnelles, notification des utilisateurs, documentation. (5) Gestion & Maintenance : mises à jour des règles et logiciels, surveillance, adaptation aux nouvelles menaces.

La phase la plus souvent négligée en production est la **Gestion & Maintenance**. Les organisations déploient un FW correctement mais ne mettent pas à jour les règles lorsque de nouvelles menaces émergent, n'élaguent pas les règles obsolètes (qui peuvent créer des ouvertures involontaires), et ne surveillent pas continuellement les logs. Un FW non maintenu dégrade progressivement sa protection.

---

**Q19.** Comparez les algorithmes Least Connections et Weighted Round-Robin pour un cluster de serveurs hétérogènes avec des requêtes de durées variables.

**Réponse :** Weighted Round-Robin assigne les poids statiquement (par l'administrateur, basé sur la capacité théorique). Si S1 est 5× plus puissant que S2, il reçoit 5× plus de requêtes. Mais si toutes les requêtes dirigées vers S1 sont des requêtes longues (transactions complexes), S1 peut être saturé pendant que S2 (moins puissant mais avec des requêtes courtes) est libre. WRR ne s'adapte pas à la charge réelle.

Least Connections sélectionne le serveur avec le moins de connexions actives en temps réel. Pour des requêtes de durées variables, il distribue naturellement vers les serveurs moins occupés. C'est plus équitable dans ce scénario.

Optimal : combiner Weighted Least Connections — prend en compte à la fois le poids (capacité) et la charge actuelle (connexions actives), divisant le nombre de connexions actives par le poids pour normaliser.

---

**Q20.** Pourquoi un Spam Honeypot cible-t-il spécifiquement les open relay SMTP et les open proxies ?

**Réponse :** Les spammeurs ont besoin de relais pour masquer leur identité et distribuer leurs envois à grande échelle. Un serveur SMTP configuré en open relay accepte et relaie des emails pour n'importe qui sans authentification — les spammeurs exploitent ces serveurs pour envoyer des millions d'emails semblant provenir d'organisations légitimes. Un proxy ouvert (open proxy) permet le même anonymat pour les connexions web.

Le Spam Honeypot simule ces ressources vulnérables (open relay SMTP + open proxy) pour attirer les spammeurs. Cela permet de : (1) collecter les listes de distribution des spammeurs, (2) analyser leurs techniques et outils, (3) identifier leurs IPs d'origine (avant anonymisation), (4) générer des blacklists, (5) comprendre les campagnes de spam en cours.

---

**Q21.** Expliquez le fonctionnement du NAPT (PAT) et décrivez l'interférence avec IPsec.

**Réponse :** NAPT (PAT) permet à plusieurs hôtes internes de partager une seule IP externe en différenciant les flux par le numéro de port source. Le routeur maintient une table de translation : IP_interne:port_src ↔ IP_externe:port_src_traduit. Les réponses arrivent sur l'IP externe avec le port traduit → le routeur remonte à l'hôte interne correspondant.

Interférence avec IPsec : IPsec ESP (Encapsulating Security Payload) chiffre et authentifie le payload **y compris les headers internes**. Lors du transit NAT, le routeur modifie l'IP source du paquet externe. À destination, l'authentification IPsec vérifie l'intégrité du paquet — la modification de l'IP source rompt le checksum d'intégrité → le paquet est rejeté. Solution : NAT-T (RFC 3948) encapsule ESP dans UDP port 4500, permettant au NAT de modifier les headers UDP sans toucher au payload ESP chiffré.

---

**Q22.** Dans une architecture Zero Trust, décrivez le rôle du Control Plane et du Data Plane. Quelle est la directionality de chaque ?

**Réponse :** Control Plane (Plan de Contrôle) : Prend les décisions d'accès. Il vérifie chaque demande d'accès selon des critères granulaires : identité de l'utilisateur, rôle, état de l'appareil (compliance), heure, géolocalisation, comportement. Il coordonne et gère l'ensemble du réseau Zero Trust. Sa direction est **top-down/centralisée** — il distribue les politiques vers le Data Plane.

Data Plane (Plan de Données) : Exécute les décisions du Control Plane. Une fois qu'une demande d'accès est approuvée par le Control Plane, le Data Plane est configuré pour **n'accepter le trafic que du client spécifiquement vérifié**. Il applique les politiques en temps réel. Sa direction est **perpendiculaire au flux de données** — il intercepte et filtre.

Interaction : le Control Plane dit "cet utilisateur peut accéder à cette ressource depuis cet appareil" → le Data Plane configure les règles pour laisser passer uniquement cette connexion spécifique.

---

**Q23.** Comparez les DMZ single firewall et dual firewall. Quelle configuration recommanderiez-vous si l'attaquant est supposément sophistiqué avec accès à des zero-day firewalls ?

**Réponse :** Single FW : 3 interfaces (WAN/LAN/DMZ), un seul FW gère tout. SPOF — si compromis, toutes les zones sont exposées simultanément.

Dual FW : deux FW encadrent la DMZ. FW1 entre Internet et DMZ, FW2 entre DMZ et LAN. Pas de SPOF — un attaquant doit compromettre les deux FW.

Face à un attaquant sophistiqué avec zero-days : recommander dual FW **avec FW de fabricants différents**. Si l'attaquant possède un zero-day pour FortiGate, un deuxième Palo Alto Networks sera immunisé contre ce même zero-day. La diversité des technologies augmente le coût d'exploitation pour l'attaquant — il doit disposer de zero-days pour deux plateformes différentes simultanément.

---

**Q24.** Expliquez le concept de VIP (Virtual IP) dans le clustering de Load Balancers et décrivez le processus de failover.

**Réponse :** Le VIP est une adresse IP virtuelle partagée par le cluster LB, exposée aux clients. Les clients envoient toutes leurs requêtes au VIP sans savoir quel nœud LB physique traite leurs requêtes.

En opération normale : l'Active LB détient le VIP et répond à toutes les requêtes. Le Passive LB surveille l'Active via heartbeat (envois périodiques de messages de "je suis vivant").

Failover : l'Active LB tombe (plus de heartbeat reçu par le Passive). Après timeout configurable, le Passive LB prend le VIP (ARP gratuit pour notifier le réseau de la nouvelle association MAC/VIP). Le Passive devient le nouvel Active et reprend le traitement du trafic de manière transparente pour les clients. Les sessions en cours peuvent être perdues selon la configuration (synchronisation d'état entre LBs possible pour éviter cela).

---

**Q25.** Scénario intégré : Un attaquant a compromis un poste utilisateur interne. Décrivez comment chaque couche de défense (de Ch2 à Ch9) peut détecter ou limiter l'attaque.

**Réponse :**

**Ch2 — Segmentation/ZT :** La micro-segmentation limite les ressources que le poste compromis peut atteindre. Zero Trust vérifie chaque tentative d'accès du poste — comportement anormal (accès à des ressources inhabituelles) déclenche une révocation d'accès.

**Ch3 — Firewall :** Un FW interne entre segments bloque les tentatives de connexion vers des sous-réseaux non autorisés depuis le poste compromis. Les ACLs sur les VLANs limitent le mouvement latéral.

**Ch4 — IDS/IPS :** Le HIDS sur le poste compromis détecte les processus anormaux et connexions sortantes vers des IPs C2. Le NIDS derrière le FW interne voit le trafic latéral et détecte des signatures d'exploit ou des anomalies comportementales.

**Ch5 — Honeypot :** En sondant le réseau interne à la recherche de ressources vulnérables, l'attaquant atteint le honeypot de production → alerte immédiate (tout accès au honeypot est une anomalie).

**Ch6 — Proxy :** Si l'attaquant tente d'exfiltrer des données via HTTP, le proxy inspecte le payload et peut détecter des patterns d'exfiltration. Les connexions vers des domaines non catégorisés ou nouvellement enregistrés sont bloquées.

**Ch7 — VPN :** Si l'attaquant tente d'établir un tunnel VPN non autorisé, le FW bloque les ports VPN non autorisés. Le split-tunneling contrôlé empêche le trafic de contourner le proxy.

**Ch9 — LB :** Limite indirectement l'impact — si l'attaquant tente une attaque DDoS interne contre des serveurs, le LB absorbe et limite le débit. La supervision du LB peut détecter des patterns de trafic inhabituels.

---

## 13. Matrices de décision rapide

### Matrice : Choix de la technologie de détection d'intrusion

| Scénario | Recommandation |
|----------|----------------|
| Réseau avec trafic chiffré TLS/VPN | HIDS + IDS hybride |
| Environnement avec beaucoup de faux positifs IDS | Anomaly-based → tuner la baseline |
| Attaque zero-day attendue | Anomaly-based + Stateful Protocol Analysis |
| Besoin de précision maximale sur attaques connues | Signature-based |
| Réseau distribué multi-sites | IDS Distribué |
| Audit post-incident / forensics | IDS périodique (interval-based) + logs HIDS |

### Matrice : Choix du type de Proxy

| Besoin | Proxy adapté |
|--------|-------------|
| Déploiement massif sans config client | Transparent |
| Contrôle granulaire par utilisateur/application | Non-transparent (Explicite) |
| Tunneling protocoles non-HTTP | SOCKS5 |
| Anonymat navigateur | Anonyme (Elite) |
| Protection serveurs web internes | Reverse Proxy |
| Cache HTTP + filtrage contenu | Squid (Forward Proxy) |

### Matrice : Choix d'algorithme Load Balancer

| Contexte | Algorithme |
|----------|-----------|
| Serveurs identiques, requêtes courtes uniformes | Round-Robin |
| Serveurs de capacités différentes | Weighted Round-Robin |
| Sessions de durées variables | Least Connections |
| Grand nombre de serveurs similaires, volume élevé | Random Connections |
| Applications stateful (panier e-commerce) | Session Affinity |
| Persistance par IP utilisateur | Source IP |
| Cache cohérent (même URL → même serveur) | Hash (URL) |

### Matrice : Choix de la topologie VPN

| Contexte | Topologie |
|----------|-----------|
| Multi-sites, coût optimisé | Hub-and-Spoke |
| 2 datacenters connectés directement | Point-to-Point |
| Réseau critique, redondance maximale | Full Mesh |
| Secteur bancaire, isolation absolue des branches | Star |
| Télétravail individuel | Client-to-Site |

### Référence rapide : couches OSI des technologies

| Technologie | Layer(s) |
|-------------|----------|
| Packet Filtering | L3-L4 |
| Circuit-Level Gateway | L5 |
| Application Proxy / Gateway | L7 |
| Stateful Multilayer Inspection | L3-L7 |
| VLAN (802.1Q) | L2 |
| NAT | L3 |
| VPN IPsec | L3 |
| VPN SSL/TLS | L4-L7 |
| SOCKS | L5 |
| Proxy HTTP | L7 |
| IDS NIDS | L3-L7 (selon méthode) |
| Load Balancer L4 | L4 |
| Load Balancer L7 | L7 |

---

## 14. Misconception Graveyard

### ❌ "La segmentation physique est la méthode la plus utilisée"
✅ La segmentation logique (VLAN) est la plus utilisée en pratique. La physique est la plus sécurisée en isolation mais son coût et sa complexité la rendent rare.

### ❌ "Un IDS bloque le trafic malveillant"
✅ Un IDS passif **alerte uniquement** — il ne bloque pas. C'est l'IPS (in-line) qui bloque. Un IDS actif peut répondre automatiquement mais reste out-of-band.

### ❌ "L'IPS remplace l'IDS"
✅ L'IPS est une **extension** de l'IDS — il fait tout ce que fait un IDS plus la prévention. Ce ne sont pas des systèmes distincts.

### ❌ "Un honeypot protège directement le réseau"
✅ Un honeypot **n'offre pas de protection directe**. C'est un outil de Threat Intelligence. Il n'alerte pas en temps réel pour stopper une attaque et ne bloque rien.

### ❌ "Le proxy transparent requiert une configuration client"
✅ **L'inverse** : le proxy transparent ne requiert **aucune configuration client**. C'est le proxy non-transparent (explicite) qui requiert une configuration par application.

### ❌ "L2TP chiffre le trafic"
✅ L2TP seul ne chiffre PAS. Il ne fait que tunneliser. C'est **L2TP/IPsec** qui assure le chiffrement.

### ❌ "Le Trusted VPN chiffre les données"
✅ Le Trusted VPN garantit l'**intégrité du chemin** (circuits dédiés chez l'opérateur) mais ne chiffre **pas** les données. C'est le Secure VPN qui chiffre.

### ❌ "Zero Trust protège le réseau"
✅ Zero Trust **protège les accès**, pas le réseau. La distinction est fondamentale — ZT supprime la notion de périmètre de confiance.

### ❌ "Le Faux Positif est le type d'alerte le plus dangereux"
✅ Le **Faux Négatif** est le plus dangereux — une vraie attaque non détectée. Les faux positifs causent de l'alert fatigue mais ne laissent pas une attaque passer inaperçue.

### ❌ "NAT = Firewall"
✅ NAT **fonctionne comme** un FW pour les connexions non-initiées de l'extérieur mais ce n'est pas sa fonction. NAT n'inspecte pas le contenu, n'a pas de politique de sécurité, et interfère avec IPsec.

### ❌ "Session Affinity et Source IP sont la même chose"
✅ Session Affinity utilise les **cookies HTTP** (L7). Source IP utilise l'**adresse IP source** (L4). Mécanismes et niveaux différents, même objectif (persistance).

### ❌ "Un NIDS peut inspecter le trafic VPN chiffré"
✅ Un NIDS est aveugle sur le trafic chiffré. Il voit des données chiffrées incompréhensibles. Solution : SSL inspection ou HIDS.

### ❌ "Weighted Round-Robin s'adapte automatiquement à la charge"
✅ Les poids sont assignés **manuellement par l'administrateur**. WRR est statique. C'est Least Connections qui s'adapte dynamiquement en temps réel.

### ❌ "Bastion Host = Firewall"
✅ Le bastion host est un **système durci** qui fonctionne **en conjonction avec** le firewall. Il ne le remplace pas. Placer un bastion sur le réseau interne est explicitement déconseillé.

### ❌ "Random Connections est purement aléatoire"
✅ Random Connections sélectionne **2 serveurs aléatoirement** puis applique **Least Connections** entre ces 2. Il y a une couche de logique de charge en dessous.

---

*Masterpiece compilé depuis CH2, CH3, CH4, CH5, CH6, CH7, CH9 — Niveau expert pour examen de master en sécurité réseau.*