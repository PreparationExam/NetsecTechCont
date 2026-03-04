# CH2 MASTERY — Segmentation Réseau & Sécurité

---

## CŒUR DU CHAPITRE

**Espace problématique :** Les réseaux "plats" traditionnels placent tous les actifs sur un seul domaine de diffusion. Si la sécurité du périmètre échoue, un attaquant gagne un mouvement latéral sans restriction. La segmentation réseau corrige ceci en subdivisant le réseau en zones isolées, contraignant le rayon d'explosion et forçant le mouvement latéral à travers des points d'étranglement contrôlés.

**Pourquoi c'est important en sécurité réseau :** La segmentation est la base structurante d'une défense en profondeur. Sans elle, un seul hôte compromis peut pivoter vers l'ensemble du réseau. Avec elle, même une brèche réussie du périmètre n'entraîne qu'une exposition partielle.

---

## CONCEPTS

### 1. Réseau plat (Flat Network)

**Définition :** Une architecture réseau dans laquelle tous les hôtes résident sur un seul domaine de couche 2/couche 3 sans barrières de segmentation interne, ce qui entraîne une communication illimitée hôte-à-hôte.

**Mécanisme :** Tous les appareils partagent le même domaine de diffusion. Les messages ARP, les diffusions DHCP et le trafic latéral circulent librement. Un point d'extrémité compromis peut atteindre directement n'importe quelle autre ressource.

**Pourquoi cela existe / menace abordée :** Simplicité héritée. Aucune exigence de sécurité initiale. La menace qu'elle ne parvient pas à adresser : le mouvement latéral après brèche du périmètre.

**Idée fausse courante :** Les étudiants confondent "plat" avec "petit". Un réseau plat peut être grand — il s'agit de l'absence de segmentation interne, pas de la taille.

---

### 2. Segmentation réseau (Segmentation réseau)

**Définition :** La pratique consistant à diviser un réseau en segments plus petits et isolés pour limiter la communication inter-segments, limitant ainsi la propagation des attaques et contraignant l'accès aux ressources sensibles.

**Mécanisme :**
1. Le réseau est divisé en fonction de la classification de sécurité, de la fonction ou du niveau de confiance.
2. La communication entre segments n'est autorisée que par le biais d'appareils contrôlés (pare-feux, routeurs).
3. Les systèmes n'ayant pas besoin d'interagir sont placés sur des segments séparés.

**Pourquoi cela existe :** Adresse le problème du mouvement latéral. Même si un attaquant franchit le périmètre, il ne peut pas accéder librement à d'autres segments.

**Avantages de sécurité (5 officiels du cours) :**
- **Sécurité renforcée** — isole le trafic entre segments
- **Meilleur contrôle d'accès** — limite l'accès aux ressources réseau spécifiques
- **Surveillance améliorée** — active la journalisation, détection des connexions internes non autorisées
- **Meilleures performances** — réduit le trafic de diffusion en limitant les hôtes par sous-réseau
- **Meilleure isolation des problèmes** — confine les incidents à un seul sous-réseau

**Idée fausse courante :** La segmentation n'est pas seulement une mesure de sécurité — elle améliore également les performances en réduisant la taille du domaine de diffusion.

---

### 3. Types de Segmentation

#### 3a. Segmentation physique

**Définition :** Division d'un réseau en composants physiques distincts utilisant une infrastructure matérielle séparée (commutateurs dédiés, câblage, pare-feux par segment).

**Mécanisme :** Chaque segment dispose de ses propres commutateurs physiques, câblage et pare-feux optionnels dédiés. Les segments ne communiquent que par leurs commutateurs/routeurs respectifs.

**Exemple :** Serveurs Web sur Switch A, serveurs DB sur Switch B, serveurs FTP sur Switch C — physiquement isolés.

**Avantages :** Méthode d'isolation la plus sécurisée — aucun contournement logique possible.

**Inconvénients :**
- Coûteux (plus de matériel, d'espace, de câblage)
- Conflits de trafic potentiels
- Complexe à mettre en œuvre et maintenir
- Chaque segment a besoin de ses propres connexions réseau et pare-feux

**⚠️ PIÈGE EXAMEN :** La segmentation physique est décrite comme "l'une des plus sécurisées" — mais cela ne signifie PAS qu'elle est la plus utilisée. Son coût et sa complexité rendent la segmentation logique préférée en pratique.

---

#### 3b. Segmentation logique (VLANs)

**Définition :** Isolation logique des segments réseau utilisant les réseaux locaux virtuels (VLAN) à la couche 2, indépendamment de l'emplacement physique des appareils.

**Mécanisme :**
1. Un commutateur géré est configuré avec plusieurs VLAN (chacun avec un ID VLAN).
2. Les ports du commutateur sont assignés à des VLAN — les appareils du même VLAN communiquent comme sur du matériel isolé.
3. Les pare-feux peuvent être partagés sur plusieurs VLAN.
4. Un routeur (ou commutateur de couche 3) est nécessaire pour le routage inter-VLAN.

**Norme IEEE :** 802.1Q (norme d'étiquetage VLAN)

**Avantages :**
- Aucun nouveau matériel requis
- Flexible — les groupements indépendants de l'emplacement physique
- Contrôle le trafic de diffusion
- Active les groupes de travail virtuels
- Supprime les frontières physiques entre utilisateurs

**Inconvénients (non dans les diapositives mais critique pour l'examen) :** Les attaques de type VLAN hopping sont possibles si les ports du commutateur sont mal configurés (double-tagging, exploitation de port trunk).

**Idée fausse courante :** Les VLAN ne sont PAS une solution de sécurité complète en eux-mêmes. Sans ACL et politiques de routage inter-VLAN, un VLAN mal configuré fournit une fausse sécurité.

---

#### 3c. Virtualisation réseau (Network Virtualization — NV)

**Définition :** Le processus d'abstraction des ressources réseau du matériel physique, soit en agrégant plusieurs réseaux physiques en un réseau virtuel unique, soit en divisant un seul réseau physique en plusieurs réseaux virtuels indépendants.

**Mécanisme :**
- Une couche de virtualisation se situe au-dessus de la couche physique (sous-jacente) et présente des réseaux virtuels (superposés) aux utilisateurs et systèmes.
- Les réseaux virtuels peuvent avoir des schémas d'adressage IP identiques sans conflit.
- La bande passante est divisée en canaux indépendants, assignés dynamiquement en temps réel.

**Distinction clé par rapport au VLAN :**
- Les VLAN opèrent à la couche 2 sur une seule infrastructure physique.
- La virtualisation réseau crée des instances de réseau logique complet (avec leur propre routage, sécurité, services) au-dessus d'une infrastructure physique partagée.

**Infrastructure sous-jacente vs Superposée :** Infrastructure sous-jacente = infrastructure physique. Superposée = instances de réseau virtuel fonctionnant dessus.

**Avantages :**
- Utilisation efficace, flexible et évolutive des ressources
- Sépare le domaine administratif du domaine virtuel
- Isolation du trafic et de l'information entre utilisateurs
- Plusieurs réseaux virtuels sur une seule infrastructure physique

---

### 4. Hôte bastion (Hôte bastion)

**Définition :** Un système spécialement durci placé à la frontière du réseau, conçu pour résister aux attaques en exposant une surface d'attaque minimale tout en agissant comme intermédiaire contrôlé entre un réseau externe non fiable (Internet) et un réseau interne fiable (intranet).

**Mécanisme :**
1. Le système d'exploitation est durci (tous les services non essentiels désactivés, aucun compte utilisateur).
2. Dispose de deux interfaces : publique (orientée Internet) et privée (orientée intranet).
3. Tout le trafic entrant/sortant le traverse.
4. Il ne traite pas directement les requêtes de service — il les transmet aux serveurs internes et relève les réponses.
5. Génère des journaux d'audit ; les programmes de vérification d'intégrité détectent les modifications non autorisées.

**Pourquoi il existe :** Fournit un point d'accès unique et rigoureux. Force tout accès externe à traverser un point auditable.

**Propriétés de sécurité :**
- Aucun compte utilisateur (prévient l'exploitation d'une connexion locale)
- NFS désactivé (prévient l'accès non autorisé aux fichiers)
- Les moniteurs automatisés scannent les journaux et alertent sur l'activité suspecte (par ex. 3 échecs de connexion avec différents identifiants)
- Deux copies de journaux maintenues ; une sur serveur dédié
- La vérification d'intégrité par checksum détecte la modification de logiciels

**Placement :** Mieux placé dans une DMZ (réseau périmétrique), avec un routeur de filtrage de paquets entre l'hôte bastion et l'intranet pour une couche de sécurité supplémentaire.

**Flux de trafic :** Internet → Pare-feu → Hôte bastion → Intranet (PAS Internet → Intranet directement)

**⚠️ PIÈGE EXAMEN :** L'hôte bastion N'est PAS un remplacement de pare-feu. Il fonctionne en conjonction avec les pare-feux. Aussi, le placer sur le réseau interne est explicitement déconseillé.

---

### 5. Types de Bastions

| Type | Description | Propriété clé |
|------|-------------|------------------|
| Single-interface | 1 interface réseau, tout le trafic via interface unique | Plus simple, séparation moins sécurisée |
| Multi-interface | ≥2 interfaces réseau, peut séparer interne/externe | Meilleure séparation réseau |
| Hôte bastion interne | Situé à l'intérieur du réseau interne | Contrôle les échanges internes |
| Dual-interface sans routage | Plusieurs connexions qui ne s'acheminent pas entre elles | Agit comme pare-feu complet ; critique : le routage DOIT être désactivé |
| Machine sacrifiée | Exécute les services risqués/inconnus ; jetable | PAS réutilisable après compromission ; les utilisateurs ne doivent PAS se sentir à l'aise dessus |
| Hôte de services externes | Héberge les services exposés en externe | Séparé du bastion principal |
| Pare-feu tout-en-un | Appareil tout-en-un | Combine fonctions bastion + pare-feu |

**⚠️ PIÈGE EXAMEN sur machine sacrifiée :** "Ces machines ne sont pas réutilisables" — c'est une déclaration exacte ciblée par l'examen. Aussi : les utilisateurs ne doivent PAS se sentir à l'aise sur les machines sacrifiées par conception.

**⚠️ PIÈGE EXAMEN sur Dual-interface sans routage :** La propriété de sécurité critique est que le routage EST DÉSACTIVÉ entre les deux interfaces. Si le routage est activé, il cesse de fonctionner comme prévu.

---

### 6. Zone démilitarisée (DMZ)

**Définition :** Un petit segment réseau isolé positionné entre le réseau interne privé d'une organisation et un réseau externe public non fiable, hébergeant les services qui nécessitent une accessibilité externe tout en empêchant l'accès direct externe aux ressources internes.

**Mécanisme :**
- Héberge les services orientés publics (serveurs Web, Email, DNS).
- Le trafic externe peut atteindre les hôtes DMZ mais NE PEUT PAS atteindre directement le réseau interne depuis la DMZ.
- Si un hôte DMZ est compromis, seules les données de cet hôte sont exposées — pas le réseau interne.

**Règle de base — Politiques de trafic DMZ :**
- Réseau interne ↔ DMZ : bidirectionnel (pour sauvegarde, authentification AD) ou unidirectionnel
- Externe (Internet) → DMZ : autorisé sur ports spécifiques (80, 25, 443)
- DMZ → Réseau interne : BLOQUÉ
- Externe → Interne : BLOQUÉ

**Serveurs généralement placés en DMZ :** Serveurs web, Serveurs de messagerie, Serveurs DNS, Serveurs FTP, Serveurs proxy

**⚠️ PIÈGE EXAMEN :** "Les hôtes DMZ ne peuvent pas se connecter au réseau interne" — c'est la règle canonique. Soyez précis : le trafic bidirectionnel DMZ↔Interne est l'exception (par ex. authentification AD), pas la défaut.

---

### 7. DMZ : Pare-feu unique vs double pare-feu

| Critère | DMZ pare-feu unique | DMZ double pare-feu |
|-----------|--------------------|-----------|
| Pare-feux | 1 (3 interfaces : WAN, LAN, DMZ) | 2 (pare-feu public + pare-feu interne) |
| Interfaces | 3 (trois jambes) | 2 par pare-feu |
| Point unique de défaillance | OUI — si pare-feu tombe en panne, toutes les zones exposées | NON — protection redondante |
| Niveau de sécurité | Modéré | Maximum |
| Complexité | Faible | Élevée |
| Coût | Faible | Élevé |
| Cas d'utilisation | PME, contrainte budgétaire | Entreprise, environnements haute sécurité |

**Anatomie du pare-feu trois jambes :**
- Interface 1 : FAI/Internet (externe)
- Interface 2 : Réseau interne
- Interface 3 : DMZ

---

### 8. Trafic Est-Ouest et Nord-Sud

**Trafic Nord-Sud :**
- Direction : Verticale — entre clients externes et serveurs du datacenter
- Flux : Client → Internet → Pare-feu → Switch spine → Switch leaf → Serveur
- "Sud" = requete entrant dans le datacenter
- "Nord" = réponse quittant le datacenter
- Les pare-feux traditionnels inspectent principalement ce trafic

**Trafic Est-Ouest :**
- Direction : Horizontale — entre serveurs AU SEIN d'un datacenter ou entre datacenters
- Inclut : commun commun serice-to-service, commun VM-to-VM, commun microcommun service-to-microcommun service
- Volume : PLUS GRAND que le trafic Nord-Sud
- Surface d'attaque : PLUS GRANDE — car la plupart des solutions de sécurité se concentrent sur le Nord-Sud
- Problème : Virtualisation accrue → trafic Est-Ouest accru → latence accrue

**⚠️ PIÈGE EXAMEN :** Le trafic Est-Ouest est PLUS volumineux que le Nord-Sud, mais reçoit MOINS d'attention de sécurité historiquement. Cette asymétrie est le concept clue d'examen ici.

**Mesures de sécurité pour Est-Ouest :** Micro-segmentation, SDN, pare-feux intra-datacenter, politiques de sécurité par segment.

**Topologie du datacenter :**
- Switch leaf : couche d'accès, connecté directement aux serveurs
- Switch spine : couche principale, interconnectant tous les switches leaf

---

### 9. Micro-segmentation

**Définition :** La pratique consistant à diviser un réseau de datacenter en zones de sécurité granulaires isolées au niveau de la charge de travail ou de l'application individuelle, permettant de définir des politiques de sécurité fines pour chaque micro-segment.

**Mécanisme :** Appliqué aux couches 4-7 (contrairement aux VLAN traditionnels à la couche 2). Utilise des politiques définies par logiciel pour contrôler le trafic entre charges de travail spécifiques.

**Objectif :** Réduit la surface d'attaque Est-Ouest ; limite le mouvement latéral au sein du datacenter même si le périmètre est franchi.

**Implémentation :** Généralement via SDN (Software Defined Networking) ou contrôles au niveau hyperviseur.

---

### 10. Réseaux à confiance zéro (Zéro Trust)

**Définition :** Un modèle de sécurité qui opère selon le principe "ne jamais faire confiance, toujours vérifier" — aucun utilisateur, appareil ou connexion réseau n'est intrinsèquement fiable indépendamment de son origine (interne ou externe au périmètre réseau).

**Principe principal :** "Ne fais confiance à personne et valide avant de fournir un service ou d'accorder un accès."

**⚠️ DISTINCTION EXAMEN CRITIQUE :** "Le Zéro Trust ne protège pas le réseau, il protège les accès." — Ceci est explicitement déclaré dans le matériel du cours. Zéro Trust est centré sur l'accès, pas centré sur le réseau.

**Mécanisme :**
1. **Plan de contrôle** (plan de contrôle cloud) : Coordonne et gér le plan de données. Vérifie chaque demande d'accès. Applique des politiques granulaires (rôle, heure, type d'appareil).
2. **Plan de données** : Une fois approuvée par le plan de contrôle, configuré pour n'accepter le trafic que du client spécifique vérifié.

**Technologies intégrées avec Zéro Trust :**
- Chiffrement (Encryption)
- Authentification multifacteur (MFA)
- Gestion des accès à priviléges (PAM — Privileged Access Management)
- Micro-segmentation

**Pourquoi cela existe :** La sécurité du périmètre traditionnelle suppose que les utilisateurs internes sont fiables. Zéro Trust élimine cette hypothération, reconnaissant que :
- Le réseau peut être compromis
- Les comptes légitimes peuvent êtred'une hypothération
- Les menaces internes existent

**Cartographie des politiques commerciales :** Zéro Trust permet aux organisations d'appliquer l'accès du moins privilégé — les employés accédent uniquement aux ressources nécessaires pour leur fonction.

---

## COMPARISON TABLES

### Table 1: Physical vs Logical vs Network Virtualization

| Criterion | Physical Segmentation | Logical (VLAN) | Network Virtualization |
|-----------|----------------------|----------------|----------------------|
| Isolation mechanism | Physical hardware separation | VLAN tags (802.1Q) | Virtualization layer (overlay) |
| Hardware required | Dedicated per segment | Shared managed switches | Shared physical infrastructure |
| Cost | High | Low | Medium-High |
| Flexibility | Low | Medium | High |
| Security level | Highest | Medium | High (with proper config) |
| Can share same IP space? | No | No | Yes |
| New hardware needed? | Yes | No | No (uses existing) |
| Layer | L1/L2 | L2 | L2-L7 |

---

### Table 2: Single-homed vs Multi-homed Bastion

| Criterion | Single-homed | Multi-homed |
|-----------|-------------|-------------|
| NICs | 1 | ≥2 |
| Network separation | None (all through one NIC) | Separates internal/external |
| Security | Lower | Higher |
| Complexity | Lower | Higher |

---

### Table 3: DMZ Single FW vs Dual FW

| Criterion | Single Firewall | Dual Firewall |
|-----------|----------------|---------------|
| Cost | Lower | Higher |
| SPOF | Yes | No |
| Security | Moderate | Maximum |
| Admin complexity | Lower | Higher |
| Interfaces | 3 (three-legged) | 2+2 |

---

### Table 4: North-South vs East-West Traffic

| Criterion | North-South | East-West |
|-----------|-------------|-----------|
| Direction | Vertical | Horizontal |
| Origin | External client | Internal server/VM |
| Traditional security coverage | High | Low (historically neglected) |
| Volume | Lower | Higher |
| Attack surface | Smaller | Larger |
| Primary threat | External attackers | Lateral movement, insider threats |
| Mitigation | Perimeter firewall | Micro-segmentation, SDN, internal FW |

---

### Table 5: Zero Trust vs Traditional Perimeter Security

| Critère | Périmètre traditionnel | Zéro Trust |
|---------|----------------------|-----------|
| Modèle de confiance | Confiance interne, méfiance externe | Aucune confiance par défaut |
| Vérification | Au point d'entrée | Continue, chaque demande |
| Trafic interne | Implicitement approuvé | Toujours vérifié |
| Ce qu'il protège | Le réseau | L'accès |
| Gère les menaces internes | Mal | Oui |
| Technologies | Pare-feu, DMZ | MFA, PAM, micro-segmentation, chiffrement |

---

## QUESTIONS D'EXAMEN OUVERTES + RÉPONSES MODÈLE VALUÉES

### Q1: Expliquez le concept de segmentation réseau et justifiez son rôle dans la sécurité des infrastructures modernes. Comparez les trois types de segmentation.

**Model Answer:**

La segmentation réseau est la pratique consistant à diviser un réseau informatique en sous-ensembles isolés, de sorte que les systèmes ou applications n'ayant pas besoin d'interagir soient placés dans des segments distincts. Elle corrige la vulnérabilité inhérente aux réseaux plats (flat networks), dans lesquels l'absence de barrières internes permet à un attaquant ayant franchi le périmètre d'accéder librement à toutes les ressources.

Les trois approches principales sont :

**Segmentation physique :** Elle repose sur une séparation matérielle complète — chaque segment dispose de ses propres équipements réseau, câblage et pare-feu. Elle constitue la méthode d'isolation la plus sécurisée, mais est coûteuse, consommatrice d'espace et complexe à administrer.

**Segmentation logique (VLAN) :** Elle utilise des réseaux locaux virtuels (conformément à la norme IEEE 802.1Q) pour isoler les segments au niveau de la couche 2, sans nécessiter de nouveau matériel. Les appareils appartenant au même VLAN communiquent comme dans un réseau isolé, quel que soit leur emplacement physique. Elle offre flexibilité et réduction du trafic de diffusion, mais reste vulnérable aux attaques de type VLAN hopping en cas de mauvaise configuration.

**Virtualisation du réseau :** Elle abstrait les ressources réseau du matériel physique, permettant de créer plusieurs réseaux virtuels complets (avec leurs propres espaces d'adressage, politiques de sécurité et services) sur une même infrastructure physique. Elle distingue la couche de virtualisation (overlay) de l'infrastructure sous-jacente (underlay) et permet même le partage d'espaces d'adressage IP identiques entre réseaux virtuels distincts.

En termes de sécurité, la segmentation limite la propagation des attaques (confinement), améliore le contrôle d'accès, facilite la surveillance et la journalisation, et réduit la surface d'attaque en East-West.

**Full marks:** Defines segmentation + addresses flat network problem + accurately describes all 3 types + security implications.
**Partial marks:** Missing comparison or missing security justification.
**Loses points:** Confusing VLAN with physical segmentation, or stating VLANs require new hardware.

---

### Q2: Décrivez l'architecture d'un hôte bastion et justifiez les mesures de sécurité qui le caractérisent.

**Model Answer:**

Un hôte bastion est un système informatique spécialement conçu et durci, positionné à la frontière entre un réseau public non fiable et un réseau interne sécurisé. Il constitue le seul point d'accès direct depuis Internet vers l'intranet.

**Architecture :**
- Deux interfaces réseau : une interface publique (directement exposée à Internet) et une interface privée (connectée à l'intranet).
- Il est idéalement positionné dans une zone démilitarisée (DMZ), avec un routeur de filtrage de paquets entre lui et l'intranet pour une couche de sécurité supplémentaire.

**Mesures de durcissement (hardening) justifiées :**
- **Désactivation de tous les services non essentiels :** Chaque service actif représente une surface d'attaque potentielle. Réduire les services au strict minimum réduit le risque d'exploitation.
- **Absence de comptes utilisateurs :** Un compte local permettrait à un attaquant de prendre le contrôle de la machine et de l'utiliser comme pivot vers l'intranet.
- **Désactivation de NFS :** Empêche tout accès non autorisé aux fichiers accessibles depuis Internet.
- **Journalisation avec double sauvegarde :** Les journaux constituent la seule preuve d'une tentative d'intrusion. Deux copies (dont une sur serveur dédié) préviennent leur altération.
- **Vérification de l'intégrité par checksum :** Détecte toute modification non autorisée d'un logiciel installé sur le bastion (signe d'une attaque ou compromission).
- **Moniteurs automatisés :** Analysent en continu les journaux et déclenchent des alertes sur activité suspecte (ex. : 3 échecs de connexion avec identifiants différents).

**Fonctionnement :** Le bastion ne traite pas directement les requêtes. Il les redirige vers le serveur interne approprié, qui exécute la demande et renvoie la réponse au bastion, qui la transmet à l'utilisateur externe. Cela empêche toute connexion directe entre Internet et l'intranet.

**Full marks:** Architecture dual-interface + placement DMZ + au moins 4 mesures de durcissement justifiées + description du flux de trafic.
**Partial marks:** Architecture correcte mais mesures de sécurité non justifiées.
**Loses points:** Confondre bastion host et pare-feu.

---

### Q3: Comparez les architectures DMZ à pare-feu unique et à double pare-feu. Dans quel contexte recommanderiez-vous chaque approche ?

**Model Answer:**

La DMZ (zone démilitarisée) est un réseau intermédiaire isolé hébergeant les services accessibles depuis l'extérieur (serveurs Web, DNS, messagerie) tout en protégeant le réseau interne.

**DMZ à pare-feu unique (single firewall / three-legged) :**
Le pare-feu dispose de trois interfaces : une connectée au réseau externe (FAI), une au réseau interne et une à la DMZ. Il constitue un point de défaillance unique (SPOF) : si le pare-feu est compromis, toutes les zones sont exposées simultanément. Cette architecture est moins coûteuse et plus simple à administrer.

**DMZ à double pare-feu (dual firewall) :**
Deux pare-feux distincts encadrent la DMZ. Le premier filtre le trafic entrant d'Internet vers la DMZ ; le second effectue une vérification supplémentaire avant d'autoriser le trafic vers le réseau interne. Il n'existe pas de point de défaillance unique. C'est l'architecture la plus sécurisée, mais aussi la plus complexe à administrer.

**Recommandations :**
- **Single firewall DMZ :** Adapté aux PME avec des contraintes budgétaires, des données moins sensibles et une équipe IT limitée.
- **Dual firewall DMZ :** Recommandé pour les entreprises gérant des données sensibles (données financières, médicales), soumises à des obligations de conformité (PCI-DSS, HIPAA) ou ciblées par des attaques sophistiquées. L'utilisation de pare-feux de fabricants différents dans la configuration dual-firewall est une bonne pratique supplémentaire (évite qu'une même vulnérabilité compromette les deux).

**Full marks:** Définition DMZ + description précise des deux architectures + SPOF identifié + recommandations contextualisées.
**Partial marks:** Architectures décrites mais SPOF non mentionné.
**Loses points:** Inverser les propriétés des deux architectures.

---

### Q4: Expliquez le modèle Zero Trust. En quoi diffère-t-il fondamentalement de la sécurité périmétrique traditionnelle, et quelles technologies en supportent la mise en œuvre ?

**Model Answer:**

Le modèle Zero Trust est une approche de sécurité reposant sur le principe « ne jamais faire confiance, toujours vérifier » (never trust, always verify). Il part du postulat que ni les utilisateurs, ni les appareils, ni les connexions réseau ne sont dignes de confiance par défaut — que leur origine soit interne ou externe au réseau.

**Différence fondamentale avec la sécurité périmétrique :**
La sécurité périmétrique traditionnelle distingue un intérieur sécurisé (trusted) d'un extérieur non fiable (untrusted), en concentrant les contrôles aux frontières du réseau (pare-feu, DMZ). Une fois à l'intérieur, le trafic est généralement considéré comme de confiance. Cette approche est vulnérable aux menaces internes et aux mouvements latéraux post-breach.

Le modèle Zero Trust supprime cette distinction : **il ne protège pas le réseau, il protège les accès**. Chaque tentative d'accès — qu'elle provienne de l'intérieur ou de l'extérieur — est vérifiée et authentifiée avant d'être accordée.

**Architecture Zero Trust — composants clés :**
- **Plan de contrôle (Control Plane) :** Coordonne et gère l'ensemble du réseau. Applique des politiques d'accès granulaires basées sur le rôle, l'heure, le type d'appareil. N'autorise que les demandes provenant d'utilisateurs et appareils vérifiés.
- **Plan de données (Data Plane) :** Une fois la demande approuvée par le plan de contrôle, configuré pour n'accepter le trafic que de ce client spécifique.

**Technologies supportant Zero Trust :**
- **MFA (Multi-Factor Authentication) :** Renforce la vérification d'identité au-delà du simple mot de passe.
- **PAM (Privileged Access Management) :** Contrôle et audite les accès à privilèges élevés.
- **Micro-segmentation :** Divise le réseau en sous-zones granulaires ; si une violation est détectée, empêche la propagation de l'attaque.
- **Chiffrement :** Protège les données en transit même sur des segments considérés internes.

**Full marks:** Principe never trust/always verify + distinction protection réseau vs. protection accès + description control/data plane + au moins 3 technologies.
**Partial marks:** Principe correct mais architecture non décrite.
**Loses points:** Dire que Zero Trust protège le réseau (la formulation exacte est qu'il protège les accès).

---

## LIENS DE CHAPITRE CROISÉS

- **Ch3 (Pare-feux):** L'implémentation de la DMZ repose sur les types de pare-feux (simple vs dual). Le placement du hôte bastion est étroitement lié à l'architecture du pare-feu. Le filtrage de paquets, l'inspection avec état — ce sont les mécanismes appliquant les politiques de segmentation.
- **Zéro Trust + Micro-segmentation** réapparaîtra dans tout chapitre traitant des architectures réseau modernes ou de la sécurité cloud.
- **VLAN + ACL :** Les ACL (Ch3) sont le mécanisme d'application qui rend la segmentation VLAN significative — les VLAN isolent au niveau L2, les ACL contrôlent le trafic inter-VLAN au niveau L3/L4.
