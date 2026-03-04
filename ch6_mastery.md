# CH6 — SERVEUR PROXY : FICHIER DE MAÎTRISE COMPLÈTE

---

## CHAPITRE CORE : ESPACE PROBLÈME ET ENJEUX

Un serveur proxy adresse le problème fondamental de l'**exposition directe des ressources internes à des réseaux non fiables**. Sans proxy, un client interne communique directement avec des serveurs externes — exposant son adresse IP, son OS, sa pile réseau, et rendant impossible tout contrôle centralisé du trafic sortant ou entrant.

Le proxy s'inscrit dans la doctrine **défense en profondeur (defense-in-depth)** : il constitue une couche d'inspection et d'intermédiation applicative qui opère au-dessus du filtrage de paquets (couche réseau) et en dessous du chiffrement de bout en bout. Il est le point de contrôle entre le réseau de confiance (LAN) et le réseau non fiable (Internet).

**Enjeux sécurité clés :**
- Masquage de la topologie interne (IP privées)
- Inspection du contenu applicatif (payload)
- Journalisation exhaustive pour l'audit
- Contrôle d'accès granulaire
- Interception de contenus malveillants avant qu'ils atteignent l'hôte

---

## CONCEPTS

---

### 1. Définition du Serveur Proxy

**Définition académique :**
Un serveur proxy est un système intermédiaire — matériel dédié ou logiciel — positionné entre un client et un serveur réel, qui reçoit les requêtes au nom du client, les transmet au serveur cible, puis retransmet les réponses au client. Il opère comme mandataire (proxy = mandataire en anglais juridique) en substituant son identité réseau à celle du client.

**Mécanisme étape par étape :**
1. L'hôte interne génère une requête HTTP/HTTPS vers un FQDN externe.
2. La requête est routée vers le proxy (par configuration explicite ou redirection transparente).
3. Le proxy inspecte l'en-tête ET la charge utile (payload) du paquet selon sa base de règles (ACL — Access Control List).
4. Si la règle autorise : le proxy **reconstruit** le paquet avec sa propre adresse IP source (substitution d'identité).
5. Le proxy transmet au serveur cible — le serveur voit l'IP du proxy, jamais l'IP interne du client.
6. La réponse revient au proxy, qui l'inspecte à nouveau selon les règles.
7. Le proxy reconstruit le paquet de réponse et l'envoie à l'hôte interne d'origine.

**Pourquoi ça existe :**
Les adresses RFC 1918 (192.168.x.x, 10.x.x.x, 172.16-31.x.x) ne sont pas routables sur Internet. Le proxy (couplé ou non à du NAT) permet à ces hôtes d'accéder à Internet tout en maintenant leur anonymat et en fournissant un point de contrôle centralisé.

**Ce qui est souvent mal compris :**
- Le proxy n'est PAS un simple routeur — il termine la connexion côté client ET initie une nouvelle connexion côté serveur. C'est une **double terminaison TCP** (two-ended relay), contrairement au routeur qui relaie sans terminer.
- Un proxy n'est PAS un NAT : le NAT opère au niveau réseau (couche 3), le proxy opère au niveau applicatif (couche 7). Le proxy comprend les protocoles applicatifs, le NAT non.
- Un proxy mal configuré devient lui-même un vecteur d'attaque (open proxy exploitable par des tiers).

---

### 2. Avantages du Serveur Proxy

| # | Avantage | Mécanisme sous-jacent |
|---|----------|----------------------|
| 1 | Protection de sécurité | Couche d'inspection entre client et serveur — le serveur réel n'est jamais exposé directement |
| 2 | Sécurité et confidentialité | L'IP interne est masquée ; le proxy présente sa propre IP publique |
| 3 | Amélioration de la vitesse | Mise en cache (caching) des ressources fréquemment demandées — réduction de la latence et de la bande passante |
| 4 | Journalisation avancée | Inspection payload → logs applicatifs complets (URL, User-Agent, contenu) vs logs header-only d'un firewall |
| 5 | Contrôle d'accès | Filtrage par URL, catégorie de site, plage horaire, identité utilisateur |
| 6 | Masquage des IP internes | Substitution d'adresse source — topology hiding |
| 7 | Réduction risque cookies | Inspection et filtrage des cookies malveillants avant qu'ils atteignent le navigateur |
| 8 | Authentification | Le proxy peut exiger une authentification (NTLM, Kerberos, Basic) avant de traiter les requêtes |

---

### 3. Fonctionnement Détaillé — Pipeline Complet

```
[Hôte interne 192.168.2.4]
         |
         | Requête HTTP GET http://example.com
         ↓
[PROXY SERVER]
  → Inspecte en-tête (IP src, port, protocole)
  → Inspecte payload (URL, contenu, MIME type)
  → Consulte base de règles (ACL)
  → Reconstruit paquet avec IP src = IP publique proxy
         |
         ↓
[FIREWALL] — utilise son IP publique enregistrée (ex: 24.67.233.7)
         |
         ↓
[INTERNET / Serveur cible]
  → Voit uniquement l'IP du proxy, jamais l'IP interne
         |
         ↓ Réponse
[FIREWALL]
         ↓
[PROXY SERVER]
  → Re-inspecte la réponse
  → Reconstruit le paquet de réponse
         ↓
[Hôte interne 192.168.2.4]
```

**Point critique :** La reconstruction du paquet est ce qui distingue le proxy du simple relai. Le proxy crée deux sessions TCP distinctes : une avec le client, une avec le serveur. Cela lui permet d'inspecter et modifier le contenu à la volée.

---

### 4. Proxy vs Filtre de Paquets (Firewall Stateless/Stateful)

| Critère | Serveur Proxy | Filtre de Paquets |
|---------|--------------|-------------------|
| **Niveau OSI** | Couche 7 (Application) | Couche 3-4 (Réseau/Transport) |
| **Ce qu'il examine** | Charge utile complète (payload) | Informations de routage (IP src/dst, port, protocole) |
| **Journalisation** | Logs détaillés — intégralité des paquets IP (URL, contenu, User-Agent) | Uniquement les en-têtes des paquets IP |
| **Action sur le paquet** | Reconstruit avec nouvelle IP source | Autorise ou bloque selon les règles |
| **Comportement en cas de panne** | **Toutes les communications réseau s'arrêtent** (fail-closed) | **Les paquets peuvent traverser le réseau** (fail-open — risque majeur) |
| **Connaissance des protocoles** | Comprend HTTP, FTP, SMTP, etc. | Ne comprend pas les protocoles applicatifs |
| **Protection contre** | Attaques applicatives, contenu malveillant, injection | Scans de ports, DoS réseau, spoofing IP |

**Piège d'exam :** En cas de panne, le proxy est **fail-closed** (sécurisant mais bloquant) tandis que le filtre de paquets est typiquement **fail-open** (laisse passer les paquets — vulnérable). C'est une distinction de sécurité critique.

---

### 5. Types de Serveurs Proxy

#### 5.1 Proxy Transparent

**Définition académique :**
Un proxy transparent (également appelé *proxy interceptant* ou *proxy inline*) est un dispositif d'intermédiation réseau qui intercepte les communications entre un client et un serveur sans nécessiter de configuration côté client ni que le client ait connaissance de son existence.

**Mécanisme :**
- Le routeur/firewall redirige automatiquement les flux (typiquement TCP/80 et TCP/443) vers le proxy via des règles iptables/NAT (redirection transparente).
- Le client croit communiquer directement avec le serveur — il ne configure rien.
- Côté serveur, l'en-tête HTTP `X-Forwarded-For` peut trahir la présence d'un proxy transparent.

**Caractéristiques :**
- Aucune configuration client requise → déploiement massif facilité
- Invisible pour l'utilisateur final
- Utilisé dans les réseaux d'entreprise, les FAI (filtrage parental), les hôtels/aéroports (captive portals)

**Piège :** Le cours indique "tous les clients web doivent être configurés manuellement" pour un proxy transparent — **c'est l'INVERSE**. C'est pour le proxy NON-transparent que la configuration manuelle est requise. Ne pas confondre.

**Risque sécurité :** Un proxy transparent peut être utilisé à l'insu de l'utilisateur pour intercepter et inspecter son trafic (MITM légitimé par politique d'entreprise, ou malveillant).

---

#### 5.2 Proxy Non-Transparent (Explicite)

**Définition académique :**
Un proxy non-transparent, ou proxy explicite, est un serveur proxy dont l'existence est connue du client et qui nécessite une configuration explicite du logiciel client pour rediriger les requêtes vers le proxy.

**Mécanisme :**
- Le client est configuré (manuellement ou via PAC — Proxy Auto-Configuration) pour envoyer ses requêtes au proxy.
- L'URL complète (incluant le nom d'hôte) est envoyée au proxy dans la requête HTTP.
- Le proxy possède donc le FQDN et peut effectuer des décisions de filtrage basées sur le nom de domaine.

**Services additionnels fournis :**
1. **Group Annotation Services** — Annotation et catégorisation du trafic par groupe d'utilisateurs
2. **Media Type Transformation** — Conversion de formats de médias à la volée (ex: transcoder une image)
3. **Protocol Reduction** — Simplification ou filtrage de protocoles complexes
4. **Anonymity Filtering** — Filtrage des informations d'identité dans les en-têtes HTTP

**Difficultés de déploiement :**
- Chaque programme client doit être paramétré pour rediriger vers un port unique (typiquement TCP/3128 pour Squid, TCP/8080 pour d'autres).
- Si une application ne supporte pas la configuration proxy, elle contourne le proxy — **proxy bypass** : risque de sécurité.

**Niveau de sécurité :** Plus élevé que le transparent car le contrôle est explicite et granulaire.

---

#### 5.3 Proxy SOCKS

**Définition académique :**
SOCKS (Socket Secure) est un protocole Internet standardisé par l'IETF qui permet à un client de se connecter à un serveur arbitraire via un serveur proxy SOCKS, en encapsulant le trafic réseau au niveau des sockets TCP/UDP, indépendamment du protocole applicatif.

**Versions :**
- **SOCKS4** : TCP uniquement, pas d'authentification, pas de résolution DNS côté proxy
- **SOCKS4a** : Extension — permet au proxy de résoudre les noms DNS (le client transmet le FQDN)
- **SOCKS5** (RFC 1928) : TCP + UDP, authentification (username/password, GSS-API), résolution DNS côté proxy — version courante

**Mécanisme :**
1. Le client établit une connexion TCP avec le serveur SOCKS (port 1080 par défaut).
2. Le client négocie l'authentification (si SOCKS5).
3. Le client envoie une requête CONNECT indiquant l'adresse/port cible.
4. Le serveur SOCKS évalue la requête (identité de l'utilisateur, destination) — **autorise ou rejette**.
5. Si autorisé : le serveur SOCKS **"bind"** (lie) la connexion — établit le tunnel vers la destination.
6. Les données transitent ensuite via le protocole natif (HTTP, FTP, etc.) à travers ce tunnel.

**Utilisation de sockets :** SOCKS utilise en interne des sockets pour suivre toutes les connexions individuelles des clients — chaque connexion client est identifiée et tracée séparément.

**Différence clé vs proxy HTTP :**
- Un proxy HTTP comprend et inspecte le protocole HTTP.
- SOCKS est **agnostique au protocole applicatif** — il crée un tunnel TCP/UDP sans interpréter le contenu. Il peut donc transporter n'importe quel protocole.
- SOCKS **n'a pas de capacités de mise en cache** (contrairement à un proxy HTTP avec cache).
- SOCKS **n'autorise pas les composants réseau externes** à collecter des informations sur le client source.

**Composants du package SOCKS :**
1. Un serveur SOCKS (pour l'OS spécifié)
2. Un programme client (FTP, Telnet, navigateur)
3. Une bibliothèque cliente pour SOCKS (permet aux applications de supporter SOCKS)

**Usage sécurité/attaque :** SOCKS5 est massivement utilisé dans les botnets et les infrastructures C2 (Command & Control) pour chaîner des proxies (proxy chaining) et obfusquer l'origine des attaquants. Tor utilise un protocole dérivé de SOCKS.

---

#### 5.4 Proxy Anonyme

**Définition académique :**
Un proxy anonyme est un serveur proxy qui omet ou modifie les en-têtes HTTP révélant l'identité du client (notamment `X-Forwarded-For`, `Via`, `X-Real-IP`) de sorte que le serveur cible ne peut pas déterminer l'adresse IP réelle de l'utilisateur originel.

**Niveaux d'anonymat (non dans le cours mais exam-trap) :**
| Niveau | Nom | Comportement |
|--------|-----|-------------|
| Niveau 1 | Elite/High Anonymity | Ne transmet aucun en-tête révélateur — le serveur cible ignore même qu'un proxy est utilisé |
| Niveau 2 | Anonymous | Transmet `Via` (révèle l'usage d'un proxy) mais pas `X-Forwarded-For` |
| Niveau 3 | Transparent | Transmet l'IP réelle via `X-Forwarded-For` — fournit l'anonymat du masquage d'IP mais révèle l'IP réelle |

**Avantages :**
- Navigation privée sans révéler l'identité IP
- Accès aux sites censurés (géoblocage, censure gouvernementale)

**Inconvénients :**
- Réduction de la vitesse de chargement (latence additionnelle)
- Illégal dans certains pays pour contourner la censure (Chine, Iran, Russie, etc.)
- Le proxy anonyme lui-même connaît l'IP réelle — confiance absolue requise envers le fournisseur

---

#### 5.5 Proxy Inverse (Reverse Proxy)

**Définition académique :**
Un proxy inverse est un serveur intermédiaire positionné du côté du serveur (et non du côté client), qui reçoit les requêtes des clients externes au nom d'un ou plusieurs serveurs backend, et leur transmet les réponses. Du point de vue du client, le proxy inverse est le serveur réel.

**Mécanisme :**
- Positionné entre Internet et le(s) serveur(s) web interne(s)
- Le client ignore l'existence du proxy inverse — il croit communiquer directement avec le serveur web
- Le proxy inverse peut distribuer les requêtes entre plusieurs serveurs backend (load balancing)

**Fonctions :**
- **Load balancing** : Distribution de charge entre serveurs
- **SSL termination** : Décryptage TLS au niveau du proxy (décharge les serveurs backend)
- **Caching** : Mise en cache des ressources statiques
- **Compression** : Optimisation du contenu pour accélérer le chargement
- **Protection DDoS** : Absorption des attaques avant qu'elles atteignent le serveur réel
- **WAF (Web Application Firewall)** : Inspection des requêtes HTTP malveillantes

**Différence fondamentale proxy direct vs proxy inverse :**

| Critère | Proxy Direct (Forward Proxy) | Proxy Inverse (Reverse Proxy) |
|---------|------------------------------|-------------------------------|
| **Position** | Côté client | Côté serveur |
| **Protège** | Le client (masque IP interne) | Le serveur (masque infrastructure backend) |
| **Connaissance client** | Client peut être conscient (non-transparent) ou non | Client **n'est jamais conscient** |
| **Connaissance serveur** | Serveur voit l'IP du proxy, pas du client | Serveur backend voit l'IP du proxy inverse, pas du client |
| **Cas d'usage** | Entreprises, contrôle accès sortant | CDN, datacenters, protection serveurs web |
| **Exemples** | Squid, CCProxy | Nginx, HAProxy, Cloudflare |

---

### 6. Limitations du Serveur Proxy

1. **Point de défaillance unique** : Si le proxy tombe en panne (fail-closed), **toutes les communications réseau s'arrêtent**. Nécessite une haute disponibilité (clustering, redondance).
2. **Charge de configuration** : Doit être configuré pour chaque service qu'il fournit — scalabilité limitée.
3. **Sensibilité aux modifications** : La modification des paramètres par défaut peut entraîner des dysfonctionnements.
4. **Latence additionnelle** : Le reroutage des informations introduit un délai de traitement.
5. **Chargement partiel de pages** : Le filtrage de contenus suspects peut empêcher certains éléments de page de se charger.

---

### 7. Exemple Concret : Squid Proxy

**Ce qu'est Squid :**
Squid est un proxy de mise en cache open-source pour le Web, supportant HTTP, HTTPS, FTP, et d'autres protocoles. Il est massivement déployé dans les entreprises et les FAI.

**Fonctions principales :**
- **Caching** : Stockage local des pages et ressources fréquemment demandées → réduction de la consommation de bande passante
- **Filtrage** (via SquidGuard) : Blacklists de domaines, catégorisation de contenus
- **ACL granulaires** : Contrôle par IP, réseau, heure, protocole, MIME type
- **Port par défaut** : TCP/3128

**Intégration pfSense :** Squid s'intègre dans pfSense (firewall open-source) en tant que module de proxy cache + filtrage.

---

## TABLEAU DE COMPARAISON DES TYPES DE PROXY

| Critère | Transparent | Non-Transparent | SOCKS | Anonyme | Inverse |
|---------|-------------|-----------------|-------|---------|---------|
| **Client conscient ?** | Non | Oui | Oui | Non/Oui | Non |
| **Config client requise ?** | Non | Oui (par programme) | Oui | Variable | Non |
| **Cache HTTP ?** | Oui | Oui | Non | Variable | Oui |
| **Niveau OSI** | 7 | 7 | 5 (Session) | 7 | 7 |
| **Agnostique protocole ?** | Non | Non | Oui | Non | Non |
| **Protège** | Client | Client | Client | Client | Serveur |
| **Usage type** | FAI, entreprise | Entreprise sécurisée | Tunneling, anonymat | Navigation privée | CDN, datacenter |

---

## QUESTIONS D'EXAMEN OUVERTES + RÉPONSES MODÈLES

---

### Q1 : Expliquez le fonctionnement d'un serveur proxy et distinguez-le d'un filtre de paquets. Quelles implications cela a-t-il sur la sécurité du réseau ?

**Réponse modèle :**

Un serveur proxy est un intermédiaire applicatif (couche 7 OSI) qui reçoit les requêtes des clients internes, les inspecte en profondeur — tant au niveau de l'en-tête que de la charge utile — et les retransmet au serveur cible en substituant sa propre adresse IP à celle du client. Ce processus implique une double terminaison TCP : le proxy termine la connexion côté client et initie une nouvelle connexion côté serveur, rendant l'infrastructure interne opaque pour le monde extérieur.

Un filtre de paquets, en revanche, opère aux couches 3 et 4 (réseau et transport). Il examine uniquement les informations de routage des paquets (adresse IP source/destination, numéro de port, protocole) et prend des décisions binaires d'autorisation ou de blocage selon des règles prédéfinies. Il ne comprend pas les protocoles applicatifs et ne peut pas inspecter le contenu des paquets.

Les implications sécuritaires sont les suivantes : le proxy offre une journalisation exhaustive (logs applicatifs complets) permettant un audit détaillé, tandis que le filtre de paquets ne journalise que les en-têtes. En cas de défaillance, le proxy adopte un comportement fail-closed (toutes communications stoppées) — plus sécurisant mais impactant la disponibilité — alors que le filtre de paquets adopte souvent un comportement fail-open (les paquets traversent librement), ce qui constitue un risque de sécurité majeur.

**Rubrique :**
- Mention de la couche OSI respective (couche 7 vs 3-4) : 3 pts
- Explication de la double terminaison TCP : 3 pts
- Distinction journalisation (payload vs en-tête) : 2 pts
- Comportement en cas de panne (fail-closed vs fail-open) : 2 pts
- Perd des points si : confond proxy et NAT, ou affirme que le filtre de paquets inspecte le payload

---

### Q2 : Comparez le proxy transparent et le proxy non-transparent. Dans quel contexte déploieriez-vous chacun et pourquoi ?

**Réponse modèle :**

Le proxy transparent intercepte le trafic sans que le client en ait connaissance ni nécessite de configuration. Il est implémenté au niveau du routeur ou du firewall via redirection transparente (iptables REDIRECT ou TPROXY). Son avantage principal est la facilité de déploiement à grande échelle sans intervention sur les postes clients. Son inconvénient est qu'il est limité aux protocoles qu'il peut intercepter (principalement HTTP/HTTPS) et que le trafic HTTPS nécessite une inspection SSL (SSL bumping) qui introduit des problèmes de confiance certificat.

Le proxy non-transparent (explicite) requiert une configuration explicite du client — soit manuelle application par application, soit via un fichier PAC (Proxy Auto-Configuration) déployé par GPO. Il offre un niveau de sécurité plus élevé car il permet des fonctionnalités supplémentaires : annotation de groupe, transformation de médias, réduction de protocole, filtrage pour l'anonymat. Il transmet l'URL complète incluant le FQDN, permettant des décisions de filtrage plus granulaires.

Déploiement : le proxy transparent convient aux environnements où l'on ne peut pas contrôler les configurations clients (réseaux publics, hôtels, FAI). Le proxy non-transparent convient aux environnements d'entreprise où la politique de sécurité est stricte et où un contrôle granulaire par utilisateur/application est requis.

**Rubrique :**
- Définition précise des deux types avec la notion de connaissance/configuration client : 3 pts
- Mécanisme de redirection transparent (niveau réseau) : 2 pts
- Services additionnels du non-transparent (4 services) : 2 pts
- Justification du choix de déploiement par contexte : 3 pts

---

### Q3 : Décrivez le protocole SOCKS, ses composants, et expliquez en quoi il diffère fondamentalement d'un proxy HTTP.

**Réponse modèle :**

SOCKS (Socket Secure) est un protocole standardisé par l'IETF qui opère au niveau de la couche session (couche 5 OSI). Contrairement au proxy HTTP qui comprend et inspecte le protocole HTTP, SOCKS crée un tunnel générique au niveau des sockets TCP/UDP, agnostique au protocole applicatif transporté. Il peut donc tunneliser n'importe quel protocole — HTTP, FTP, SMTP, SSH, etc. — sans en interpréter le contenu.

Le mécanisme SOCKS5 (RFC 1928) se déroule comme suit : le client établit une connexion TCP avec le serveur SOCKS sur le port 1080, négocie une méthode d'authentification (aucune, username/password, ou GSS-API), envoie une requête CONNECT avec la destination, et le serveur évalue la requête selon l'identité de l'utilisateur et la destination demandée. Si la requête est valide, le serveur "bind" la connexion et les données transitent ensuite de manière transparente.

Les trois composants du package SOCKS sont : (1) un serveur SOCKS pour l'OS cible, (2) un programme client supportant SOCKS (FTP, Telnet, navigateur), (3) une bibliothèque cliente SOCKS permettant aux applications de l'utiliser.

Différences clés avec un proxy HTTP : SOCKS ne dispose pas de capacités de mise en cache ; il n'autorise pas les composants réseau externes à collecter des informations sur le client source ; il supporte UDP (SOCKS5) contrairement à un proxy HTTP pur.

**Rubrique :**
- Définition avec couche OSI et agnosticisme protocolaire : 3 pts
- Mécanisme de connexion (handshake, bind) : 3 pts
- Trois composants listés correctement : 2 pts
- Différences SOCKS vs HTTP proxy (cache, UDP, opacité) : 2 pts

---

### Q4 : Quelles sont les limitations d'un serveur proxy du point de vue de la sécurité et de la disponibilité ? Comment un architecte réseau devrait-il en tenir compte ?

**Réponse modèle :**

Un serveur proxy présente plusieurs limitations critiques. Du point de vue de la disponibilité, le proxy constitue un point de défaillance unique (SPOF — Single Point of Failure) : sa panne entraîne l'interruption totale des communications réseau (comportement fail-closed). Cela contraste avec un filtre de paquets dont la panne laisse le trafic passer librement. L'architecte doit donc prévoir une redondance (clustering actif-actif ou actif-passif, load balancing entre proxies).

Du point de vue de la sécurité, si le proxy n'est pas correctement sécurisé, il devient lui-même un vecteur d'attaque — un proxy mal configuré peut devenir un "open proxy" exploitable par des tiers pour anonymiser des activités malveillantes. La charge de configuration est également significative : chaque service doit être configuré individuellement, augmentant la surface d'erreur de configuration. La modification des paramètres par défaut peut provoquer des dysfonctionnements difficiles à diagnostiquer.

Enfin, le proxy introduit une latence additionnelle (reroutage, inspection, reconstruction de paquets) et peut provoquer un chargement partiel des pages lors du filtrage de contenus suspects.

L'architecte réseau doit déployer le proxy en haute disponibilité, maintenir une politique stricte de configuration, auditer régulièrement les règles ACL, et mettre en place des mécanismes de surveillance (monitoring) des logs pour détecter les tentatives d'abus du proxy.

**Rubrique :**
- Identification du SPOF et comportement fail-closed : 3 pts
- Risque open proxy / surface d'attaque : 2 pts
- Charge de configuration et erreurs potentielles : 2 pts
- Mesures d'atténuation (HA, monitoring, audit) : 3 pts

---

## LIENS INTER-CHAPITRES

- **Firewall (chapitres sur les firewalls)** : Le proxy complète le firewall — le firewall filtre au niveau réseau/transport, le proxy filtre au niveau applicatif. Dans l'architecture typique : Client → Proxy → Firewall → Internet.
- **NAT** : Souvent confondu avec le proxy. NAT opère couche 3 (translation d'adresses), proxy opère couche 7 (intermédiation applicative). Peuvent coexister.
- **VPN** : Un VPN chiffre le tunnel bout-en-bout ; un proxy inspecte le contenu. SOCKS5 + TLS peut simuler un VPN mais sans les garanties cryptographiques.
- **IDS/IPS** : Un proxy peut intégrer des capacités IDS (inspection de contenu malveillant) — point de synergie.
- **Authentification** : Le proxy non-transparent intègre l'authentification utilisateur (NTLM, Kerberos) — lien avec les chapitres AAA.
