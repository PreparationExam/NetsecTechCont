# CH3 MASTERY — Firewalls

---

## CŒUR DU CHAPITRE

**Espace problématique :** La connexion de réseaux privés à des réseaux publics crée une frontière de trafic non fiable. Sans point d'application contrôlé, n'importe quel agent externe ou interne peut communiquer avec n'importe quelle ressource interne. Les pare-feux résolvent ce problème en implémentant le filtrage du trafic basé sur des règles au niveau de la frontière et en interne.

**Pourquoi c'est important :** Le pare-feu est l'appareil d'application principal de la politique de sécurité réseau. Comprendre ses types, technologies, capacités et limites est fondamental pour la conception d'architecture et l'analyse d'attaque/défense.

---

## CONCEPTS

### 1. Pare-feu — Définition & Fonctionnement

**Définition :** Un pare-feu est un dispositif logiciel, matériel ou combiné placé entre un réseau privé protégé et un réseau public non fiable. Il surveille et filtre tout le trafic entrant et sortant selon un ensemble de règles prédéfinies, autorisant ou bloquant le trafic selon les critères configurés.

**Critères de filtrage :**
- Nature du trafic (type de trafic)
- Adresses IP source et destination
- Protocoles utilisés
- Numéros de ports

**Mécanisme :** Chaque paquet passant est évalué par rapport à un ensemble de règles ordonné (de haut en bas). Si une correspondance est trouvée, l'action configurée (autoriser/refuser) est prise. Si aucune règle ne correspond, la politique par défaut s'applique (généralement tout refuser).

**Principales utilisations :**
- Protéger les services internes du trafic externe non autorisé
- Restreindre l'accès de l'hôte interne aux services externes
- Supporter NAT (partager une connexion Internet unique avec des adresses IP privées)

---

### 2. Types de pare-feux par form factor

#### 2a. Pare-feu matériel (pare-feu matériel)

**Définition :** Un appareil de sécurité physique dédié placé au périmètre du réseau, généralement intégré dans des routeurs haut débit ou déployés en tant qu'appareils autonomes.

**Mécanisme :** Utilise le filtrage de paquets en lisant les en-têtes de paquets (IP source/destination, port). Inspecte à la vitesse de transmission en utilisant du matériel dédié.

**Exemples :** Cisco ASA, FortiGate

**Protège :** Généralement le LAN privé dans son ensemble.

**Avantages :** OS dédié (surface d'attaque réduite), réponse plus rapide, débit élevé, interférence minimale (peut être arrêté/déplacé/reconfiguré sans affecter le réseau).

**Inconvénients :** Plus cher que les pare-feux logiciels, plus difficile à implémenter et configurer, nécessite plus d'espace physique et de câblage.

---

#### 2b. Pare-feu logiciel (pare-feu logiciel)

**Définition :** Une application logicielle installée sur des hôtes individuels, opérant entre la couche application et les composants réseau du système d'exploitation, filtrant le trafic pour cette machine spécifique.

**Mécanisme :** Intercepte toutes les requêtes réseau vers l'ordinateur, les valide par rapport aux règles. Se situe dans le chemin critique application/réseau.

**Exemples :** Norton, McAfee, Kaspersky

**Cas d'utilisation :** Utilisateurs individuels à domicile, travailleurs mobiles en dehors du réseau d'entreprise.

**Avantages :** Moins cher, facile à installer et configurer, inclut les contrôles de confidentialité, filtrage web, filtrage de contenu.

**Inconvénients :** Consomme les ressources système (peut ralentir les performances), difficile à désinstaller, non adapté aux environnements haut débit.

**⚠️ REMARQUE :** Meilleure pratique = configurer à la fois les pare-feux matériel ET logiciel pour une protection optimale.

---

#### 2c. Pare-feu basé sur l'hôte (Pare-feu basé sur l'hôte)

**Définition :** Un pare-feu logiciel installé sur un ordinateur individuel qui filtre le trafic pour cette machine spécifique, souvent intégré au système d'exploitation.

**Exemples :** Pare-feu Windows, iptables, UFW

**Mécanisme (multi-couches) :**
1. Analyse des paquets aux couches OSI 3 (Réseau) et 4 (Transport)
2. Vérification de l'adresse MAC, adresse IP, port source/destination
3. Filtrage de paquets avec état
4. Vérification de la couche application

**Avantages :** Protection spécifique au dispositif indépendamment de l'emplacement ; sécurité interne ; les VMs peuvent transporter leur pare-feu hôte vers les environnements cloud ; règles personnalisables.

**Inconvénients :** Pas évolutif pour les grands réseaux ; si l'attaquant accède à l'hôte, il peut désactiver le pare-feu ; doit être remplacé si la bande passante dépasse la capacité ; coûteux pour les grandes organisations (installation par serveur).

---

#### 2d. Pare-feu basé sur le réseau (Pare-feu basé sur le réseau)

**Définition :** Un pare-feu matériel qui filtre le trafic entrant et sortant pour un LAN interne entier, agissant comme première ligne de défense du périmètre réseau.

**Exemples :** pfSense, Smoothwall, Cisco SonicWall, Netgear ProSafe, D-Link

**Mécanisme :** Opère au niveau réseau, filtrant toutes les données le traversant. Redirige le trafic vers les serveurs proxy pour la gestion de transmission de données.

**Avantages :** Aucune installation par serveur nécessaire ; évolutif ; haute disponibilité ; détecte le trafic malveillant au niveau du réseau ; peu de personnel nécessaire pour la gestion.

**Inconvénients :** N'adresse PAS les vulnérabilités application/VM ; ne protège PAS les communications hôte-à-hôte dans le même VLAN ; nécessite des compétences techniques avancées ; sous-performant pour les petites organisations.

**⚠️ Meilleure pratique :** Combiner pare-feux basés sur le réseau + basés sur l'hôte. Si un attaquant contourne la sécurité réseau, les pare-feux hôte restent comme deuxième couche.

---

#### 2e. Pare-feu externe (Pare-feu externe)

**Définition :** Un pare-feu placé entre le réseau protégé et le réseau public pour limiter l'accès, valider le trafic et effectuer NAT entre les adresses IP internes et publiques.

**Cas d'utilisation spécial :** Protège les appareils obsolètes/heritage manquant de capacités de pare-feu internes. Placé entre l'appareil heritage et le LAN.

**Exemples :** Floodgate Defender (Icon Labs), Firebox M440 (WatchGuard)

**Principal avantage :** Peut être mis à jour indépendamment de l'appareil heritage. Si l'appareil heritage est compromis, le pare-feu externe peut le détecter, arrêter la propagation et bloquer la communication Internet.

---

#### 2f. Pare-feu interne (Pare-feu interne / Pare-feu de segmentation interne)

**Définition :** Un pare-feu déployé à l'intérieur du réseau interne pour protéger un segment réseau d'un autre, applique l'inspection avec état et les politiques de filtrage au trafic traversant le réseau interne.

**Placement :** Entre deux segments de la même organisation OU entre deux organisations partageant un réseau.

**Objectif :** Empêcher l'activité malveillante localisée de se propager à d'autres segments. Installé oà différents niveaux d'accès sont requis.

**Avantages :** Isole les serveurs critiques des utilisateurs internes et externes ; bloque la communication hôte-à-hôte ; visibilité réseau interne améliorée ; gère plus de trafic que les pare-feux périmétriques ; contient le trafic VPN.

**Inconvénients :** Nécessite la création de sous-réseaux supplémentaires ; impratique pour les systèmes mobiles ; coûteux.

---

### 3. Technologies de Firewall

**OSI Layer Mapping:**

| OSI Layer | Technologies |
|-----------|-------------|
| Application (7) | VPN, Application Proxy |
| Presentation (6) | VPN |
| Session (5) | VPN, Circuit-Level Gateway |
| Transport (4) | VPN, Packet Filtering |
| Réseau (3) | VPN, NAT, Filtrage de paquets, Inspection multi-couches avec état |
| Liaison de données (2) | VPN, Filtrage de paquets |
| Physique (1) | N/A |

---

#### 3a. Filtrage de paquets (Filtrage de paquets)

**Définition :** La fonction de pare-feu la plus basique, opérant à la couche OSI 3 (couche réseau / couche IP). Évalue chaque paquet individuellement par rapport à un ensemble de règles basé sur l'information d'en-tête, sans maintien de l'état de la session.

**Mécanisme :**
1. Le paquet arrive au pare-feu.
2. L'en-tête est extrait et comparé à l'ensemble de règles (de haut en bas).
3. Si correspondance trouvée → action (accepter/refuser/supprimer).
4. Si aucune correspondance → politique par défaut.
5. L'entrée est enregistrée dans la table de session (table de suivi de connexion).

**Critères de filtrage :**
- Adresse IP source
- Adresse IP destination
- Port source TCP/UDP
- Port destination TCP/UDP
- Bits de code TCP (drapeaux SYN, ACK)
- Protocole
- Direction (entrant/sortant)
- Interface (fiable/non fiable)

**Trois règles de configuration :**
1. Accepter uniquement les paquets sécurisés, refuser tous les autres (liste blanche)
2. Refuser uniquement les paquets dangereux, accepter tous les autres (liste noire)
3. Demander à l'utilisateur de décider pour les paquets inconnus (interactif)

**Nature :** **Sans état** — aucune mémoire de session. Chaque paquet inspecté indépendamment.

**Avantages :** Coût faible, impact de performance minimal.

**Limitations :** Ne peut pas vérifier si les paquets appartiennent à des sessions établies ; vulnérable à l'usurpation d'IP ; ne peut pas inspecter le contenu de la couche application ; pas de suivi de session.

**⚠️ PIÈGE EXAMEN :** "Sans état" = filtrage de paquets. "Avec état" = inspection avec état. Connaissez bien la différence.

---

#### 3b. Passerelle de niveau circuit (Passerelle de niveau circuit)

**Définition :** Un pare-feu qui opère à la couche OSI 5 (couche session) ou la couche TCP du modèle TCP/IP. Il valide les échanges de poignée de main TCP pour déterminer si une session demandée est légitime, sans inspecter le contenu des paquets.

**Mécanisme :**
1. Le client envoie une requête au serveur externe.
2. La passerelle de niveau circuit intercepte la requête.
3. Transmet le paquet à la destination, en modifiant l'adresse source (de sorte que la destination pense que le trafic provient de la passerelle).
4. À la réception de la réponse, la passerelle vérifie que l'IP source correspond à la destination originale.
5. Si correspondance → transmet le paquet. Si non → le rejette.

**Filtrage du trafic :** Effectué selon les règles de session (par ex. session initiée par un ordinateur reconnu). Le trafic inconnu autorisé uniquement jusqu'à la couche 3 (niveau TCP).

**Propriété clé :** Le pare-feu apparaît comme la source de tout le trafic — **masque les informations internes du réseau des hôtes externes**.

**PAS autonome :** Fonctionne en conjonction avec d'autres pare-feux (filtrage de paquets + proxy d'application).

**Avantages :** Masque les données du réseau privé ; ne filtre pas les paquets individuels ; aucun proxy séparé par application nécessaire ; facile à mettre en œuvre.

#### 3c. Passerelle de niveau application (Passerelle de niveau application / Proxy d'application)

**Définition :** Un pare-feu qui filtre les paquets à la couche OSI 7 (couche application), capable d'inspecter les commandes spécifiques à l'application et le contenu spécifique au protocole.

**Mécanisme:**
1. La requête client est interceptée par la passerelle.
2. La passerelle évalue au niveau application — protocole, type d'application, commandes spécifiques (GET, POST).
3. Si conforme à la politique de sécurité → transmet à la destination. Sinon → bloque.
4. Nécessite l'authentification avant d'autoriser les paquets.

**Critères de filtrage :** Type d'application (p. ex. navigateur Web), protocole (p. ex. FTP), ou les deux.

**Caractéristique particulière :** Peut filtrer des commandes HTTP spécifiques (GET/POST), détecter les transferts FTP, bloquer Telnet — contrôle granulaire du protocole impossible aux couches inférieures.

**Modèle de communication :** La communication directe entre client et serveur NE se produit PAS — tout passe par le proxy/passerelle. Cela procure la transparence — les utilisateurs perçoivent une connexion directe au serveur.

**Proxy de mise en cache de contenu :** Variante qui met en cache les données fréquemment accédées localement, réduisant la charge réseau.

**Avantages :** Journalisation excellente (comprend les protocoles d'application) ; mise en cache réduit la charge réseau ; authentification au niveau utilisateur ; protège contre les implémentations TCP/IP faibles (génère de nouveaux paquets IP pour les clients).

**Inconvénients :** Plus lent que les services sans proxy ; chaque service peut nécessiter des serveurs séparés ; peut nécessiter des modifications client/application ; la complexité le rend vulnérable aux attaques DoS ; ne peut pas filtrer le trafic SSL/TLS chiffré si non configuré pour l'inspection SSL.

**⚠️ PIÈGE EXAMEN :** Proxy d'application ≠ Passerelle de niveau circuit. Le proxy d'application inspecte le CONTENU au niveau 7. La passerelle de niveau circuit valide uniquement la SESSION au niveau 5. Les deux masquent les détails du réseau interne, mais pour des raisons différentes et à des couches différentes.

---

#### 3d. Inspection Multi-Couches avec État (Inspection multi-couches avec état)

**Définition :** Une technologie de pare-feu qui combine le filtrage de paquets, la vérification de session et l'inspection au niveau application, en maintenant une table d'état des connexions établies pour évaluer si les paquets entrants appartiennent à des sessions valides.

**Mécanisme:**
1. Le paquet arrive → vérifié par rapport à la table d'état.
   - S'il appartient à une session établie → autorisé.
   - S'il s'agit d'une nouvelle connexion → les règles de filtrage de paquets sont appliquées.
2. Les paquets ne correspondant pas aux règles de filtrage → rejetés au niveau réseau.
3. Les paquets passant le filtre L3 → vérifiés par rapport à la table d'état au niveau TCP.
4. Les paquets n'appartenant à aucune session active → bloqués au niveau TCP.
5. Les paquets restants → filtrés au niveau application.

**Table d'état :** Enregistre les détails de connexion (IP source/destination, ports, protocole, état de connexion).

**Avantages :** Haute sécurité ; meilleures performances que le proxy d'application (pas de surcharge proxy) ; transparence pour les utilisateurs finaux ; combine filtrage de paquets + inspection application.

**Inconvénients :** Coûteux ; nécessite des compétences d'administration avancées.

**Key distinction from stateless packet filtering:** Stateless = each packet independent. Stateful = packets evaluated in context of their session.

**Examples:** Cisco Adaptive Security Appliance (ASA) implements stateful multilayer inspection.

**⚠️ PIÈGE EXAMEN :** L'inspection avec état « corrige la faiblesse des filtres de paquets sans état ». Elle suit les sessions, elle peut donc détecter :
- Les attaques par injection mid-session
- Les paquets qui n'appartiennent à aucune session établie
- Les variations d'attaques SYN flood

---

#### 3e. Proxy d'Application (Proxy d'application)

Ceci est étroitement lié à la passerelle de niveau application mais distinct dans les détails d'implémentation.

**Définition :** Un serveur intermédiaire qui agit comme mandataire entre un client et un service externe, gérant toute la communication au nom du client. Il intercepte les requêtes et les réponses, en appliquant le filtrage de sécurité pour les services/protocoles spécifiques.

**Mécanisme :** Un proxy FTP n'autorisera que le trafic FTP ; tous les autres protocoles sont bloqués. Le proxy intercepte, évalue et ré-établit toutes les connexions.

**Transparence :** Les utilisateurs perçoivent une connexion directe au serveur réel ; le serveur perçoit une connexion directe à l'utilisateur. Les deux perçoivent le proxy comme l'autre point de terminaison — c'est l'avantage clé.

**Mise en cache du proxy :** Stocke des copies de données fréquemment accédées — réduit la charge sur les connexions réseau.

---

#### 3f. NAT (Traduction d'adresses réseau)

**Définition :** Un mécanisme qui sépare les adresses IP en deux ensembles (privé interne + public externe) et traduit entre eux, dissimulant la structure du réseau interne et forçant les connexions à travers un point de contrôle.

**Mécanisme :**
- La machine interne envoie un paquet → routeur (NAT) modifie l'IP source pour qu'elle apparaisse comme une adresse externe valide.
- La machine externe répond → NAT modifie l'IP destination, convertissant l'adresse externe en adresse interne correcte.

**Schémas NAT :**

| Type | Description | Propriétés |
|------|-------------|------------|
| Mappage statique 1:1 | Une IP externe par IP interne, toujours la même traduction | Lent, pas d'économie IP, peut être statique ou dynamique |
| Mappage d'adresse dynamique | IP externe assignée dynamiquement, pas de modification de port | Limite l'accès Internet simultané au nombre d'IP externes |
| NAPT / Surcharge NAT (PAT) | Plusieurs machines internes partagent une IP externe via la traduction de ports | Le plus courant — permet le mappage plusieurs-à-un |
| Allocation dynamique d'adresse+port | Paire IP+port externe assignée dynamiquement par connexion | Efficacité d'IP maximale |
| NAT de destination / Redirection de port | L'IP du routeur est utilisée pour les communications externes, redirige vers le serveur interne | Utilisé pour l'accès depuis l'extérieur aux services internes |

**Fonction de sécurité :** Peut agir comme filtrage de paquets — n'autorise que les connexions initiées depuis le réseau interne ; bloque les connexions initiées en externe.
**Avantages :** Renforce le contrôle du pare-feu sur les connexions sortantes ; limite le trafic entrant aux réponses ; masque la configuration du réseau interne.

**Inconvénients :** L'estimation de la durée de la traduction est impossible à perfectionner ; interfère avec les systèmes de chiffrement/authentification ; l'allocation dynamique de ports peut perturber le filtrage de paquets.

**⚠️ PIÈGE EXAMEN :** NAT est une technologie de routage qui fonctionne COMME une technologie de pare-feu lorsqu'elle est combinée à un pare-feu. Ce N'EST PAS un pare-feu autonome. Aussi : NAT interfère avec IPsec (car IPsec vérifie les adresses IP pour l'intégrité — NAT les modifie).

---

#### 3g. VPN (Réseau privé virtuel)

**Définition :** Un réseau privé construit sur une infrastructure publique (Internet) utilisant l'encapsulation et le chiffrement pour permettre la transmission sécurisée de données sensibles sur des réseaux non fiables.

**Mécanisme (cycle complet) :**
1. Le trafic est **chiffré**
2. La **protection d'intégrité** est vérifiée
3. Les **nouveaux paquets sont encapsulés** et envoyés par Internet
4. L'appareil récepteur **désencapsule**
5. L'**intégrité vérifiée** à nouveau
6. Le trafic est **déchiffré**

**Relation VPN + pare-feu :** Le VPN effectue le chiffrement/déchiffrement EN DEHORS du périmètre de filtrage des paquets, de sorte que les paquets chiffrés d'autres sites peuvent toujours être inspectés. Les pare-feux sont des outils pratiques pour ajouter la fonctionnalité VPN.

**Utilisation :** Interconnexion WAN ; permet aux ordinateurs sur différents réseaux de communiquer de manière sécurisée sur Internet public.

**Avantages :** Masque tout le trafic en transit ; fournit une protection des données chiffrées ; support d'accès à distance ; protection contre les attaques externes.

**Inconvénients :** Opérer sur un réseau public signifie que l'utilisateur reste vulnérable aux attaques ciblant le réseau de destination.

---

#### 3h. NGFW (Pare-feu de prochaine génération)

**Définition :** Un appareil de sécurité réseau de troisième génération qui étend les capacités des pare-feux traditionnels avec IPS, reconnaissance/contrôle d'application, gestion d'identité utilisateur et inspection profonde de paquets jusqu'à la couche 7.

**Pare-feu traditionnel vs NGFW :**
- Traditionnel : Opéré principalement aux couches 3-4 (en-têtes IP, TCP/UDP)
- NGFW : S'étend à la couche 7 (inspection de contenu applicatif)

**Caractéristiques principales :**
- Reconnaissance et contrôle d'application
- Authentification basée sur l'utilisateur
- Protection contre les malwares
- Inspection avec état
- IPS intégré (Système de prévention d'intrusions)
- Gestion d'identité (contrôle utilisateur/groupe)
- Modes pont et routé
- Intégration d'informations de menace externe

**Capacités avancées :**
- DPI (Inspection profonde de paquets)
- Inspection de trafic chiffré (déchiffrement SSL/TLS)
- Gestion de bande passante / QoS
- Intégration d'informations de menace
- ATP (Protection avancée contre les menaces)
- Inspection antivirus

**Avantages du NGFW :**
- Sécurité au niveau application (IDS + IPS intégrés)
- Gestion à partir d'une console unique (vs config manuelle complexe dans pare-feu traditionnel)
- Protection multi-couches (L2-L7)
- Infrastructure simplifiée (appareil autorisé unique)
- Maintient le débit réseau indépendamment du nombre de protocoles de sécurité
- Solution tout-en-un (antivirus, anti-ransomware, antispam, sécurité endpoint)
- Contrôle d'accès basé sur les rôles (RBAC)

---

### 4. ACL (Listes de contrôle d'accès)

**Définition :** Un ensemble de règles configurées sur un pare-feu ou un routeur qui autorise ou bloque le trafic réseau basé sur des critères de correspondance évalués de haut en bas.

**Évaluation :** Les règles sont évaluées de haut en bas — les règles les plus spécifiques doivent être en haut.

**Couches et types d'ACL :**
- Couche 2 : ACL basées sur l'adresse MAC
- Couche 3 : ACL basées sur l'adresse IP
- Couche 4 : ACL basées sur ports TCP/UDP

**Catégories d'ACL :**

| Type | Critères | Cas d'utilisation |
|------|----------|-------------------|
| ACL standard | IP source uniquement | Protection basique, petits réseaux, traitement faible |
| ACL étendue | IP src + IP dst + MAC + Ports | Filtrage multi-critères, environnements d'entreprise |

**⚠️ PIÈGE EXAMEN :** ACL standard = IP SOURCE UNIQUEMENT. ACL étendue = plusieurs critères. C'est une distinction QCM classique.

---

### 5. iptables

**Définition :** Un outil de pare-feu en ligne de commande Linux (basé sur l'hôte) qui autorise ou bloque le trafic réseau en utilisant des chaînes de règles dans des tables.

**Installation :** `sudo apt-get install iptables`

**Trois chaînes :**
- **INPUT :** Gère les connexions entrantes
- **FORWARD :** Redirige les connexions entrantes vers leur destination finale
- **OUTPUT :** Gère les connexions sortantes

**Commandes clés :**
- `sudo iptables -L -n -v` — lister toutes les règles avec sortie verbose
- `iptables -t nat -L -v -n` — lister les règles de la table NAT
- `-A INPUT -p tcp --syn -m state --state NEW -j DROP` — supprimer les nouvelles connexions non TCP SYN
- `-A INPUT -p tcp --tcp-flags ALL -j DROP` — bloquer l'analyse XMAS
- `-A INPUT -f -j DROP` — supprimer les paquets fragmentés

---

### 6. UFW (Pare-feu non compliqué)

**Définition :** Une interface simplifiée pour iptables sur les systèmes Linux.

**Commandes clés :**
- `sudo apt-get install ufw` — installer
- `sudo ufw status verbose` — vérifier l'état (par défaut : inactif)
- `sudo ufw enable` — activer
- `sudo ufw default deny incoming` — refuser par défaut le trafic entrant
- `sudo ufw default allow outgoing` — autoriser par défaut le trafic sortant
- `sudo ufw allow ssh` / `sudo ufw allow 22` — autoriser SSH
- `sudo ufw allow 80/tcp` — autoriser HTTP
- `sudo ufw deny from 10.10.10.24` — refuser une IP spécifique
- `sudo ufw allow from 198.51.100.0/24` — autoriser un sous-réseau

---

### 7. Capacités & Limites d'un pare-feu

**Capacités :**
- Analyse tout le trafic le traversant par rapport aux règles
- Autorise uniquement le trafic explicitement autorisé ; bloque tout le reste par défaut
- Filtre à la fois le trafic entrant ET sortant
- Gère l'accès public aux ressources privées
- Enregistre toutes les tentatives d'accès ; déclenche des alarmes sur tentatives hostiles/non autorisées
- Supporte NAT
- Fonctions : défense de la passerelle, application de la politique de sécurité, masquage d'adresses internes, détection/signalement de menaces, séparation de zones

**Limitations (CRITIQUE EXAMEN — souvent testées) :**
- Ne protège pas contre les attaques par porte dérobée (par ex. un employé interne malveillant coopérant avec un attaquant externe)
- Ne protège pas contre les attaques internes
- Ne peut pas compenser une mauvaise conception réseau ou une mauvaise configuration
- Ne remplace PAS antivirus/antimalware
- Ne bloque PAS les virus nouveaux/zero-day
- Ne protège pas contre les attaques d'ingénierie sociale
- Ne prévient PAS l'abus de mot de passe
- Ne peut pas bloquer les attaques des couches supérieures de la pile de protocoles
- Ne peut pas bloquer les attaques via ports courants ou applications courantes
- Ne peut pas protéger contre les attaques de connexion à distance (modem, VPN non sécurisé)
- Ne comprend PAS le trafic tunnelisé (encapsulé dans d'autres protocoles)
- Peut restreindre les services légitimes (FTP, Telnet, NIS, accès Internet)
- Peut devenir un goulot d'étranglement si TOUT le trafic doit le traverser
- Peut avoir des problèmes de performances si la vitesse de traitement < vitesse de l'interface réseau

**⚠️ PIÈGE EXAMEN :** Les professeurs adorent demander "Contre quoi un pare-feu NE protège pas ?" Connaissez cette liste à fond.

---

### 8. Déploiement d'un pare-feu — 5 phases

1. **Planification :** Evaluation des risques → identification des menaces → architecture du pare-feu → définition de la politique de sécurité (règles, placement, approche de configuration)
2. **Configuration :** Configuration du matériel/logiciel ; politiques de sécurité ; mécanismes de journalisation ; systèmes d'alerte
3. **Tests :** Vérifier la conformité des règles au comportement souhaité ; détection de bugs ; validation de la fiabilité
4. **Déploiement :** Alignement avec les politiques de sécurité organisationnelles ; notification des utilisateurs ; documentation de tous les changements ; approche par phases pour détecter les conflits entre politiques
5. **Gestion & Maintenance :** Maintenance de l'architecture ; mises à jour des règles ; mises à jour logicielles ; surveillance des composants ; mise à jour des règles lorsque de nouvelles menaces émergent ou que les besoins organisationnels changent

---

### 9. Bonnes pratiques de sécurité du pare-feu

**Pratiques clés :**
- Filtrer les ports inutilisés/vulnérables (approche de défense en profondeur)
- Configurer les comptes admin avec des ID uniques (ne jamais utiliser root/admin pour les services de pare-feu)
- Ensemble de règles par défaut-refus — autoriser uniquement les services essentiels
- Changer les mots de passe par défaut ; utiliser des mots de passe forts (protection contre le brute-force)
- Optimiser l'ensemble de règles (autoriser uniquement les applis importantes, bloquer le reste)
- Synchroniser l'heure via NTP pour les horodatages syslog précis
- Surveiller régulièrement les journaux du pare-feu
- Analyser les journaux d'actions autorisées ET refusées
- Sauvegarde régulière (au moins mensuellement) ; sauvegarder avant/après les changements de règles
- Audits de sécurité annuels au minimum
- Implémenter des stratégies supplémentaires pour la protection contre les attaques internes (logiciel de surveillance pour détecter l'activité interne suspecte)

**7 recommandations (diapositive) :**
1. Flux de travail standardisé pour les demandes de changement
2. Ne pas définir de règles conflictuelles ; éliminer les existantes
3. Supprimer les règles inutilisées/obsolètes
4. Nettoyer et optimiser l'ensemble de règles
5. Planifier les audits de sécurité réguliers
6. Conserver les journaux de changements de règles/config
7. Informer l'administrateur de la politique de sécurité de tous les changements ; les documenter

**Faire / Ne pas faire :**
- FAIRE : Déployer un pare-feu robuste ; limiter les applis sur le pare-feu ; contrôler l'accès physique ; évaluer les capacités ; considérer l'intégration du flux de travail
- NE PAS FAIRE : Négliger l'évolutivité ; compter uniquement sur le filtrage de paquets ; ignorer les exigences matérielles ; réduire les mesures de sécurité supplémentaires ; déployer sans chiffrement SSL

---

## TABLEAUX COMPARATIFS

### Tableau 1 : Résumé de toutes les technologies de pare-feu

| Technologie | Couche OSI | État de session | Inspection de contenu | Vitesse | Niveau de sécurité |
|-----------|-----------|---------------|-------------------|-------|------------------|
| Filtrage de paquets | L3-L4 | Non (sans état) | Non | Plus rapide | Basique |
| Passerelle de niveau circuit | L5 | Session uniquement | Non | Rapide | Modéré |
| Passerelle de niveau application | L7 | Oui | Oui (commandes app) | Lent | Élevé |
| Inspection multi-couches avec état | L3-L7 | Oui | Oui | Modéré | Très élevé |
| Proxy d'application | L7 | Oui | Profond | Plus lent | Maximum |
| NGFW | L3-L7 | Oui | DPI + Chiffré | Variable | Maximum |

---

### Tableau 2 : ACL standard vs ACL étendue

| Critère | ACL standard | ACL étendue |
|-------|-------------|------------|
| Critères | IP source uniquement | IP src, IP dst, MAC, Ports |
| Frais de traitement | Faible | Plus élevé |
| Cas d'utilisation | Simple, petits réseaux | Entreprise, politiques complexes |
| Granularité | Faible | Élevée |

---

### Tableau 3 : Pare-feu matériel vs Pare-feu logiciel

| Critère | Pare-feu matériel | Pare-feu logiciel |
|-------|------------------|----------------|
| Coût | Élevé | Faible |
| Performance | Débit élevé | Consomme ressources système |
| Évolutivité | Élevée | Limitée |
| Cas d'utilisation | Périmètre réseau | Hôte individuel |
| Configuration | Complexe | Simple |
| Exemples | Cisco ASA, FortiGate | Norton, McAfee, Windows FW |

---

### Tableau 4 : Inspection sans état vs Inspection avec état

| Critère | Sans état (Filtrage) | Multi-couches avec état |
|-------|---|---|
| Suivi de session | Non | Oui (table d'état) |
| Évaluation par paquet | Indépendant | Conscience du contexte |
| Détection d'attaques | Limitée | Détecte les attaques mid-session |
| Performance | Plus élevée | Légèrement plus basse |
| Coût | Faible | Élevé |

---

## QUESTIONS D'EXAMEN OUVERTES + RéPONSES MODÈLE VALUÉES

### Q1 : Comparez les technologies de pare-feu de filtrage de paquets (sans état) et d'inspection multi-couches avec état (avec état). Pourquoi le passage de l'une à l'autre constitue-t-il une avancée significative en matière de sécurité ?

**Model Answer:**

Le **filtrage de paquets sans état (stateless packet filtering)** opère à la couche réseau (couche 3 du modèle OSI). Il évalue chaque paquet de manière indépendante, en comparant les informations d'en-tête (IP source/destination, ports, protocole, flags TCP) à un ensemble de règles prédéfinies, sans conserver aucune information sur les sessions actives. Bien qu'il soit économique et performant, il présente des limitations critiques : il est incapable de distinguer un paquet appartenant à une session légitime établie d'un paquet injecté malicieusement, et ne peut pas vérifier la cohérence applicative des échanges.

**L'inspection multi-couches avec état (stateful multilayer inspection)** corrige ces limitations en maintenant une table d'états qui enregistre les informations des sessions établies (IP source/destination, ports, protocole, état de la connexion). Lorsqu'un paquet arrive :
1. Il est d'abord vérifié contre les règles de filtrage au niveau réseau.
2. S'il passe, il est contrôlé contre la table d'états — les paquets n'appartenant à aucune session active sont bloqués au niveau TCP.
3. Enfin, les paquets sont filtrés au niveau application.

Cette approche combine les avantages du filtrage de paquets (rapidité, économie) avec ceux de l'inspection applicative, offrant ainsi un haut niveau de sécurité tout en restant transparent pour les utilisateurs finaux. Les Cisco ASA en sont l'implémentation référence.

**Avancée sécuritaire :** La capacité à contextualiser chaque paquet dans sa session permet de détecter des injections de paquets hors session, des attaques de type SYN flood (pas de session correspondante), et des comportements anormaux mid-session. Le filtrage sans état ne peut détecter aucun de ces comportements.

**Full marks:** Définition précise des deux types + mécanisme de la state table + pipeline de traitement en 3 étapes + justification de l'avancée sécuritaire.
**Partial marks:** Comparaison correcte sans détail du mécanisme de traitement.
**Loses points:** Confondre "stateful" avec "application gateway" ou ne pas mentionner la state table.

---

### Q2: Expliquez le fonctionnement d'une passerelle de niveau circuit (circuit-level gateway). En quoi diffère-t-elle fondamentalement d'une passerelle de niveau application ?

**Model Answer:**

**Passerelle de niveau circuit :**

Elle opère à la couche session (couche 5 du modèle OSI), ou au niveau TCP du modèle TCP/IP. Son rôle est de valider la légitimité de la session TCP via la surveillance du handshake (SYN, SYN-ACK, ACK), sans jamais inspecter le contenu des paquets.

Mécanisme : Lorsqu'un client envoie une requête vers un serveur externe, la passerelle intercepte la demande. Elle transmet le paquet au destinataire en modifiant l'adresse source (le paquet semble provenir de la passerelle elle-même). À la réception de la réponse, la passerelle vérifie si l'IP source correspond à la destination originale. En cas de correspondance, le paquet est transmis ; sinon, il est rejeté.

Propriété clé : Elle **masque les informations internes du réseau** car toutes les communications semblent provenir du firewall. Elle ne filtre pas les paquets individuellement et ne nécessite pas de proxy distinct par application.

Limitation majeure : Elle ne peut pas analyser les contenus actifs et ne gère que les connexions TCP.

**Passerelle de niveau application :**

Elle opère à la couche application (couche 7). Elle inspecte le contenu des paquets et peut filtrer des commandes spécifiques à l'application (GET/POST HTTP, commandes FTP). Elle requiert une authentification avant d'autoriser le passage. Elle peut bloquer des protocoles entiers (FTP, Telnet, Gopher) ou des commandes spécifiques.

**Différence fondamentale :**

| Critère | Circuit-Level | Application-Level |
|---------|--------------|-------------------|
| Couche OSI | 5 (Session) | 7 (Application) |
| Inspection | TCP handshake uniquement | Contenu applicatif |
| Commandes filtrées | Non | Oui (GET, POST, FTP commandes) |
| Proxy par service | Non nécessaire | Oui |
| Performance | Plus rapide | Plus lente |

**Full marks:** Mécanisme précis des deux + distinction de couche + tableau comparatif ou équivalent + limitations des deux.
**Partial marks:** Description correcte d'une seule technologie.
**Loses points:** Affirmer que la circuit-level gateway inspecte le contenu des paquets.

---

### Q3: Quelles sont les principales limites d'un pare-feu ? Dans quel scénario d'attaque un pare-feu correctement configuré est-il inefficace ?

**Model Answer:**

Un pare-feu, même correctement configuré, présente des limites structurelles importantes :

**1. Attaques internes :** Un pare-feu filtre principalement le trafic à sa frontière. Un employé malveillant opérant depuis l'intérieur du réseau peut accéder à des ressources sans jamais traverser le pare-feu.

**2. Portes dérobées (backdoors) :** Un attaquant ayant recruté un complice interne peut contourner le pare-feu via des canaux non surveillés.

**3. Trafic tunnelisé :** Les pare-feux ne comprennent pas le trafic encapsulé dans d'autres protocoles — par exemple, un shell inversé (reverse shell) encapsulé dans du trafic HTTP sur le port 80 traversera un pare-feu qui autorise HTTP.

**4. Attaques zero-day :** Le pare-feu ne peut pas bloquer des exploits ciblant des vulnérabilités inconnues dans des applications autorisées.

**5. Ingénierie sociale :** Un utilisateur cliquant sur un lien malveillant dans un courriel initie lui-même la connexion depuis l'intérieur — le pare-feu voit du trafic sortant légitime.

**6. Ports et applications courants :** Si un attaquant utilise des ports autorisés (80, 443) ou des applications légitimes comme vecteur, le pare-feu ne peut pas distinguer le trafic malveillant du trafic légitime sans inspection profonde.

**7. Mauvaise conception réseau :** Un pare-feu ne peut pas compenser une politique de sécurité défaillante ou une architecture réseau incorrecte.

**Scénario illustratif :** Un attaquant obtient les identifiants d'un employé via phishing. Il se connecte au VPN de l'entreprise avec ces identifiants valides. Le pare-feu voit une connexion VPN authentifiée provenant d'un utilisateur autorisé — il la laisse passer. L'attaquant se trouve maintenant à l'intérieur. En l'absence de segmentation interne, il peut effectuer du mouvement latéral librement.

**Full marks:** ≥5 limites précisément décrites + scénario illustratif cohérent.
**Partial marks:** < 4 limites ou scénario absent.
**Loses points:** Mentionner des protections que le firewall FOURNIT comme si c'étaient des limites.

---

### Q4: Décrivez les cinq phases de déploiement d'un pare-feu et justifiez l'importance de chaque phase.

**Model Answer:**

**Phase 1 — Planification :**
Avant tout déploiement, une évaluation des risques doit identifier les menaces probables, leurs origines et les raisons qui les motivent. Sur cette base, une architecture de déploiement est définie, ainsi qu'une politique de sécurité précisant les règles de communication, le positionnement exact du pare-feu et son mode de configuration. Sans planification rigoureuse, le pare-feu risque d'être mal positionné ou de ne pas correspondre aux besoins réels.

**Phase 2 — Configuration :**
Paramétrage des composants matériels et logiciels, des politiques de sécurité, des mécanismes de journalisation et des systèmes d'alerte. Une mauvaise configuration est l'une des principales causes de vulnérabilités — un pare-feu mal configuré peut être plus dangereux qu'aucun pare-feu car il donne une fausse impression de sécurité.

**Phase 3 — Tests :**
Vérification que les règles du pare-feu correspondent bien aux comportements attendus et détection de bugs. Cette phase valide que les services autorisés passent et que les services bloqués sont effectivement refusés. Elle augmente la fiabilité du système avant mise en production.

**Phase 4 — Déploiement :**
Mise en service en conformité avec les politiques organisationnelles. Les utilisateurs doivent être informés. Toutes les modifications apportées doivent être documentées. Une approche par phases (pour les déploiements multi-pare-feu) permet de détecter des conflits entre politiques de différents segments.

**Phase 5 — Gestion et Maintenance :**
Maintien continu de l'architecture, mises à jour des règles et du logiciel, supervision des composants déployés. Les règles doivent être actualisées dès l'identification de nouvelles menaces ou l'évolution des besoins organisationnels. La sécurité d'un pare-feu repose sur une surveillance continue et une réactivité aux incidents.

**Full marks:** 5 phases nommées + mécanisme de chaque + justification de son importance.
**Partial marks:** Phases nommées sans justification.
**Loses points:** Oublier la phase Gestion/Maintenance (souvent négligée mais critique pour les exams).

---

## LIENS DE CHAPITRE CROISÉS

- **Ch2 (Segmentation):** L'architecture DMZ (Ch2) nécessite des décisions de placement du pare-feu (Ch3). Les pare-feux internes sont un composant clé de la segmentation réseau. NAT est utilisé dans les configurations DMZ. Les hôtes bastion travaillent conjointement avec les technologies de pare-feu décrites ici.
- **ACLs** appliquent les politiques de segmentation VLAN — les VLAN créent une séparation logique, les ACLs appliquent les règles de trafic inter-VLAN.
- **Zéro Trust** (Ch2) utilise la micro-segmentation + les pare-feux internes (Ch3) comme mécanismes d'application.
- **iptables/UFW** sont des implémentations de pare-feu basées sur l'hôte — se connectent aux concepts de sécurité basés sur l'hôte de Ch2.
