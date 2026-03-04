# CH5 — HONEYPOT : FICHIER DE MAÎTRISE COMPLÈTE

---

## CHAPITRE CORE : ESPACE PROBLÈME ET ENJEUX

Les défenses traditionnelles (firewalls, IDS, proxies) sont réactives ou filtrantes — elles bloquent ou détectent ce qui est connu. Mais elles ne répondent pas à une question stratégique fondamentale : **qui attaque, comment, avec quels outils, et quelles sont ses intentions ?**

Le honeypot adresse ce vide en inversant la logique défensive : au lieu de se protéger, on **crée délibérément une cible attrayante** pour attirer, observer et analyser les attaquants dans un environnement contrôlé, sans risque pour l'infrastructure réelle.

C'est un outil de **renseignement sur les menaces (Threat Intelligence)** — il ne bloque rien, il **apprend**. Cette distinction est critique et constitue le piège d'exam numéro un sur ce chapitre.

**Position dans la doctrine de sécurité :**
- Complément aux défenses actives (firewall, IDS/IPS, proxy)
- Source de Threat Intelligence : TTP (Tactics, Techniques, Procedures) des attaquants
- Système d'alerte précoce (Early Warning System)
- Outil légal de collecte de preuves (forensics) si correctement déployé

---

## CONCEPTS

---

### 1. Définition du Honeypot

**Définition académique :**
Un honeypot est une ressource du système d'information délibérément configurée pour être attrayante pour les attaquants, ne possédant aucune valeur de production légitime, dont le seul objectif est d'attirer, piéger, observer et analyser les comportements malveillants dirigés contre un réseau ou une organisation.

**Propriétés fondamentales (toutes obligatoires pour qualifier un système de honeypot) :**
1. **Aucune activité autorisée** — tout trafic entrant est par définition suspect
2. **Aucune valeur de production** — il ne sert aucun usage légitime
3. **Volontairement vulnérable et attrayant** — conçu pour être compromis
4. **Instrumenté** — chaque action est enregistrée (logs, captures réseau, keyloggers)

**Conséquence logique de la propriété 1 :** Tout trafic destiné au honeypot est probablement une **sonde**, une **attaque** ou une **tentative de compromission**. Le taux de faux positifs est donc théoriquement nul pour la détection — contrairement aux IDS qui génèrent de nombreux faux positifs.

**Ce que le honeypot fait :**
- Journaliser les tentatives d'accès aux ports
- Surveiller les frappes du clavier d'un attaquant (keylogging)
- Observer le comportement des attaquants
- Analyser les techniques d'attaque (TTP)
- Détecter des signaux faibles avant une attaque coordonnée

**Ce que le honeypot NE fait PAS :**
- Il ne protège pas directement le réseau
- Il ne bloque pas les attaques
- Il ne génère pas d'alertes temps-réel pour stopper une attaque en cours
- Il ne remplace pas un firewall, IDS ou proxy

---

### 2. Positionnement Architectural

**Dans le cours, le honeypot est positionné dans la DMZ :**

```
[Réseau Interne] → [Firewall] → [DMZ]
                                  ├── Serveur Web (production)
                                  └── Honeypot (leurre)
                              → [Packet Filter] → [Internet] → [Attaquant]
```

**Pourquoi la DMZ ?**
- Zone semi-publique : accessible depuis Internet mais isolée du réseau interne
- L'attaquant peut l'atteindre depuis Internet sans compromettre le réseau interne
- Si le honeypot est compromis, l'attaquant reste confiné à la DMZ
- Le Packet Filter côté Internet laisse passer le trafic malveillant volontairement vers le honeypot

**Risque critique d'architecture :** Un honeypot mal configuré peut devenir un **tremplin (pivot)** pour l'attaquant vers le réseau interne. C'est le risque principal que tout architecte doit mitiger — le honeypot doit être strictement isolé du LAN.

---

### 3. Classification par Niveau d'Interaction

C'est la classification la plus importante pour l'examen — 4 niveaux, distinctions précises.

| Type | Ce qu'il simule | Risque | Données collectées | Usage typique |
|------|----------------|--------|-------------------|---------------|
| **Faible interaction** | Nombre limité de services et d'applications | Faible | Limités (scans, tentatives basiques) | Détection rapide, déploiement facile |
| **Interaction moyenne** | Un véritable OS + applications et services | Moyen | Modérés (comportement post-connexion) | Recherche, HoneyBOT |
| **Forte interaction** | L'ensemble des services et applications d'un réseau cible | Élevé | Très riches (TTPs complets) | Recherche avancée |
| **Purs (Pure)** | Le véritable réseau de production d'une organisation cible | Très élevé | Maximaux | Émulation complète d'infrastructure |

**Gradient risque/données :** Plus l'interaction est élevée, plus les données collectées sont riches — mais plus le risque que l'attaquant utilise le honeypot comme pivot vers le vrai réseau est grand.

**Pièges d'exam :**
- "Faible interaction simule un OS complet" → FAUX — c'est l'interaction **moyenne**
- "Les honeypots purs simulent des services" → FAUX — ils **émulent** le véritable réseau de production (nuance émulation vs simulation)
- "Forte interaction = honeypot pur" → FAUX — ce sont deux niveaux distincts. Forte = ensemble des services d'un réseau cible / Pur = émulation du réseau de production réel

---

### 4. Classification par Stratégie de Déploiement

#### 4.1 Honeypots de Production

**Définition :** Honeypots déployés **au sein du réseau de production** de l'organisation, aux côtés des serveurs réels de production.

**Objectifs :**
- Détecter les attaques externes pénétrant le périmètre
- Identifier des **défaillances internes** (menaces internes, insiders)
- Identifier les **attaquants présents à l'intérieur de l'organisation**

**Point clé :** Le honeypot de production détecte les **menaces internes** — c'est sa valeur ajoutée par rapport aux défenses périmètriques. Un employé malveillant qui sonde le réseau interne déclenchera une alerte honeypot.

#### 4.2 Honeypots de Recherche

**Définition :** Honeypots à **forte interaction**, déployés par des instituts de recherche, gouvernements ou organisations militaires pour obtenir des informations détaillées sur les actions des intrus.

**Objectifs :**
- Collecter des TTPs (Tactics, Techniques, Procedures) des groupes d'attaquants avancés (APT)
- Alimenter des bases de données de threat intelligence
- Étudier les malwares, exploits zero-day, comportements post-compromission

**Distinction production vs recherche :**
| | Production | Recherche |
|-|-----------|----------|
| **Niveau d'interaction** | Faible à moyen | Forte |
| **Déployé par** | Entreprises | Instituts de recherche, gouvernements, militaires |
| **Objectif** | Détecter menaces opérationnelles | Comprendre les attaquants |
| **Détail des données** | Opérationnel | Analytique et académique |

---

### 5. Classification par Technologie de Tromperie

6 types spécialisés — chacun cible un vecteur d'attaque spécifique.

| Type | Mécanisme de tromperie | Attaque ciblée |
|------|----------------------|---------------|
| **Malware Honeypot** | Système vulnérable dans l'infrastructure réseau | Campagnes de malwares, tentatives d'infection |
| **Database Honeypot** | Fausses bases de données vulnérables | Injection SQL, énumération de BDD |
| **Spam Honeypot** | Serveurs de messagerie ouverts, proxies ouverts | Spammeurs abusant de ressources vulnérables |
| **Email Honeypot** | Fausses adresses email | Courriels malveillants, phishing, harvesting |
| **Spider Honeypot** | Pages web avec liens et contenu piégés | Robots d'indexation (crawlers), scrapers malveillants |
| **Honeynet** | Réseau de plusieurs honeypots interconnectés | Toutes capacités adversariales — profil complet |

**Points précis par type :**

**Database Honeypot** — Attaques spécifiques visées : injection SQL ET énumération de bases de données. La fausse BDD semble vulnérable et contient des données fictives attrayantes.

**Spam Honeypot** — Cible les spammeurs qui cherchent des **serveurs de messagerie ouverts** (open relay SMTP) et des **proxies ouverts** pour relayer anonymement leur spam.

**Email Honeypot** — Différent du spam honeypot : utilise de **fausses adresses email** pour attirer les courriels malveillants. Utile pour collecter des échantillons de phishing et malwares distribués par email.

**Spider Honeypot** — Cible les crawlers malveillants (bots de scraping, harvesteurs d'emails, cartographes d'infrastructure). Utilise des liens cachés invisibles aux humains mais suivis par les bots.

**Honeynet** — Réseau de PLUSIEURS honeypots interconnectés. Le plus puissant pour déterminer l'**ensemble des capacités des adversaires** — l'attaquant croit pivoter dans un vrai réseau alors qu'il est entièrement observé.

---

### 6. Outils Honeypot

**HoneyBOT :**
- Honeypot à **interaction moyenne** pour Windows
- Développé par Atomic Software Solutions
- Facile à utiliser, idéal pour la recherche en sécurité réseau
- Capture les logs de connexion : IP source, port, protocole, données échangées
- Exemple du cours : capture d'une connexion FTP (port 21) avec handshake SYN/FIN

**Autres outils mentionnés :**
- **KFSensor** (keyfocus.net) — honeypot Windows
- **MongoDB-HoneyProxy** (github.com) — honeypot pour MongoDB
- **Modern Honey Network** (github.com) — framework de déploiement d'honeynets
- **ESPot** (github.com) — honeypot Elasticsearch
- **HoneyPy** (github.com) — honeypot Python modulaire

---

## TABLEAU DE COMPARAISON : HONEYPOT vs IDS vs FIREWALL

| Critère | Honeypot | IDS | Firewall |
|---------|---------|-----|---------|
| **Rôle** | Attirer et observer | Détecter | Bloquer |
| **Protège directement ?** | ❌ Non | ❌ Non (passif) | ✅ Oui |
| **Génère des faux positifs ?** | Quasi-nul (tout trafic = suspect) | Élevé | Non applicable |
| **Valeur de production** | Aucune (intentionnellement) | Aucune | Fonctionnel |
| **Données collectées** | TTPs, comportements, outils | Signatures, anomalies | Logs d'en-têtes |
| **Détecte menaces internes ?** | ✅ Oui (honeypot de production) | Partiel | ❌ Non |
| **Couche OSI** | Toutes (instrumenté au niveau OS) | 3-7 | 3-4 (ou 7 pour proxy) |

---

## QUESTIONS D'EXAMEN OUVERTES + RÉPONSES MODÈLES

---

### Q1 : Définissez un honeypot et expliquez en quoi il diffère fondamentalement d'un système de détection d'intrusion (IDS).

**Réponse modèle :**

Un honeypot est une ressource du système d'information délibérément vulnérable et attrayante, ne possédant aucune valeur de production légitime, dont l'unique objectif est d'attirer des attaquants pour observer leur comportement, analyser leurs techniques et collecter du renseignement sur les menaces. Toute interaction avec un honeypot étant par définition non autorisée, le taux de faux positifs est théoriquement nul — contrairement à un IDS.

Un IDS (Intrusion Detection System) est un système d'inspection passive qui analyse le trafic réseau réel (trafic légitime + malveillant) et génère des alertes lorsque des signatures connues ou des anomalies comportementales sont détectées. Il opère sur du trafic de production existant et génère des faux positifs significatifs.

La différence fondamentale est la suivante : un IDS observe le trafic qui passe naturellement dans le réseau et tente de distinguer le légitime du malveillant — tâche difficile générateur de faux positifs. Le honeypot crée artificiellement une cible dont toute interaction est malveillante par définition — aucune ambiguïté possible. Cependant, le honeypot ne protège pas directement le réseau et ne bloque aucune attaque, contrairement à un IPS.

**Rubrique :**
- Définition correcte avec les 3 propriétés (aucune activité autorisée, aucune valeur de production, volontairement vulnérable) : 3 pts
- Explication de l'IDS avec notion de faux positifs : 2 pts
- Différence fondamentale (taux de faux positifs quasi-nul du honeypot) : 3 pts
- Mention que le honeypot ne protège pas directement : 2 pts

---

### Q2 : Comparez les quatre niveaux d'interaction des honeypots. Pour chaque niveau, précisez ce qui est simulé et le niveau de risque associé.

**Réponse modèle :**

Les honeypots sont classifiés en quatre niveaux selon la profondeur de l'émulation :

Le honeypot à **faible interaction** simule uniquement un nombre limité de services et d'applications d'un système ou réseau cible. Son niveau de risque est faible car l'attaquant ne peut pas réellement exécuter de code ou pivoter — mais les données collectées sont limitées aux scans et tentatives basiques.

Le honeypot à **interaction moyenne** simule un véritable système d'exploitation ainsi que des applications et services d'un réseau cible. Le niveau de risque est modéré — l'attaquant peut interagir plus profondément, fournissant des données comportementales plus riches. HoneyBOT en est un exemple.

Le honeypot à **forte interaction** simule l'ensemble des services et des applications d'un réseau cible. Le risque est élevé car l'attaquant dispose d'un environnement quasi-complet dans lequel évoluer. Les données collectées sont très riches (TTPs complets). Il est principalement utilisé pour la recherche (honeypots de recherche).

Le honeypot **pur** émule le véritable réseau de production d'une organisation cible — c'est le niveau le plus avancé, offrant une réplique fidèle de l'environnement réel. Le risque est maximal, proportionnel à la fidélité de l'émulation.

**Rubrique :**
- Description précise de chaque niveau (ce qui est simulé/émulé) : 4 pts (1 pt/niveau)
- Gradient risque correctement gradué (faible → très élevé) : 2 pts
- Distinction simulation (faible/moyen/fort) vs émulation (pur) : 2 pts
- Mention d'un exemple (HoneyBOT = interaction moyenne) : 2 pts

---

### Q3 : Expliquez la différence entre honeypots de production et honeypots de recherche, et justifiez pourquoi un honeypot de production est capable de détecter des menaces internes.

**Réponse modèle :**

Les honeypots de production sont déployés au sein même du réseau de production de l'organisation, aux côtés des serveurs réels. Leur objectif est opérationnel : détecter les attaques en cours, identifier les menaces actives dans le réseau, et lever des alertes. Étant déployés en interne, ils ont une capacité unique : identifier les défaillances internes et les attaquants présents à l'intérieur de l'organisation (menaces internes, insiders malveillants).

Les honeypots de recherche sont des honeypots à forte interaction, déployés principalement par des instituts de recherche, des gouvernements ou des organisations militaires. Leur objectif est analytique : comprendre les comportements des attaquants avancés (APT), collecter des TTPs, alimenter la threat intelligence mondiale. Ils ne visent pas une protection opérationnelle immédiate mais une compréhension approfondie de l'adversaire.

Un honeypot de production détecte les menaces internes parce qu'il est positionné sur le réseau LAN, accessible depuis les postes internes. Un employé malveillant ou un attaquant ayant compromis un poste interne qui sonde le réseau à la recherche de ressources vulnérables entrera en contact avec le honeypot — déclenchant une alerte. Aucun utilisateur légitime n'a de raison d'accéder au honeypot (aucune valeur de production), donc tout accès depuis l'intérieur est suspect.

**Rubrique :**
- Définition correcte des deux types avec objectifs distincts : 4 pts
- Justification de la détection des menaces internes (position LAN + aucune valeur légitime) : 4 pts
- Mention que les honeypots de recherche sont à forte interaction : 2 pts

---

### Q4 : Décrivez le concept de Honeynet. En quoi est-il supérieur à un honeypot isolé pour l'analyse des capacités adversariales ?

**Réponse modèle :**

Un honeynet est un réseau composé de plusieurs honeypots interconnectés, formant une infrastructure leurre complète qui simule un véritable réseau d'organisation. Contrairement à un honeypot isolé qui capture l'interaction d'un attaquant avec un seul système, le honeynet permet d'observer le comportement de l'attaquant à travers toute une infrastructure — phases de reconnaissance, de mouvement latéral, d'escalade de privilèges, d'exfiltration de données.

Sa supériorité sur un honeypot isolé repose sur trois axes : premièrement, la **complétude du profil adversarial** — l'attaquant croit évoluer dans un vrai réseau, révélant l'ensemble de ses TTPs (Tactics, Techniques, Procedures). Deuxièmement, la **détection du pivot** — en observant comment l'attaquant tente de se déplacer entre les honeypots, on comprend ses techniques de mouvement latéral. Troisièmement, la **durée d'engagement** — un réseau plus riche maintient l'attaquant occupé plus longtemps, permettant une collecte de données plus complète.

Le honeynet est particulièrement efficace pour "déterminer l'ensemble des capacités des adversaires" — il est la solution la plus puissante pour le threat intelligence avancé.

**Rubrique :**
- Définition correcte (réseau de plusieurs honeypots) : 2 pts
- Explication de la supériorité (profil complet, mouvement latéral) : 4 pts
- Mention des TTPs et threat intelligence : 2 pts
- Notion d'engagement prolongé de l'attaquant : 2 pts

---

## LIENS INTER-CHAPITRES

**Chapitre 6 (Proxy Server) :**
- Le spam honeypot cible les abus de **proxies ouverts** — lien direct avec les risques des open proxies du ch.6
- Un honeypot positionné avec un proxy inverse peut filtrer le trafic légitime pour ne laisser passer que le malveillant vers le honeypot

**Chapitres Firewall / DMZ :**
- Le honeypot est systématiquement positionné en **DMZ** dans le cours — maîtriser la DMZ est prérequis
- Le Packet Filter (côté Internet) laisse délibérément passer le trafic vers le honeypot — comportement intentionnellement inverse d'un firewall classique

**IDS/IPS (si présent dans d'autres chapitres) :**
- Honeypot ≠ IDS — complémentaires mais distincts
- Le honeypot génère quasi-zéro faux positifs vs IDS qui en génère beaucoup
- Le honeynet peut être couplé à un IDS pour corréler les alertes

**Threat Intelligence :**
- Les honeypots de recherche alimentent les bases de TTPs
- Données honeypot → IOCs (Indicators of Compromise) → règles IDS/firewall : cycle de renseignement
