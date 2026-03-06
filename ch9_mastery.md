# Chapitre 9 — Load Balancer (Équilibreur de charge) : Fichier de Maîtrise Complète

---

## CHAPITRE CORE

**Espace problème adressé :** Lorsqu'un service réseau reçoit plus de requêtes que ce qu'un seul serveur peut traiter, il y a risque de surcharge, de dégradation des performances, voire de panne totale du service. Le Load Balancer répond à ce problème en distribuant intelligemment le trafic réseau entre plusieurs serveurs, assurant disponibilité, performance et résilience.

**Pourquoi c'est critique en sécurité réseau :** Au-delà de la performance, le Load Balancer joue un rôle de défense active contre les attaques volumétriques (DoS, DDoS) en limitant le débit de requêtes et en absorbant les pics de trafic malveillant. Il est un composant architectural central dans les infrastructures sécurisées modernes.

---

## CONCEPTS

---

### 1. Définition du Load Balancer

**Définition académique :** Un équilibreur de charge (load balancer) est un dispositif réseau responsable de la distribution du trafic entrant entre plusieurs serveurs d'un système distribué, selon des algorithmes prédéfinis, dans le but d'optimiser les performances, d'assurer la haute disponibilité et de se protéger contre les attaques basées sur le débit.

**Mécanisme de base :**
1. Un client envoie une requête (HTTP, API, connexion TCP...)
2. La requête arrive au Load Balancer (point d'entrée unique)
3. Le Load Balancer sélectionne un serveur selon son algorithme configuré
4. Il transmet la requête au serveur sélectionné
5. Le serveur traite et renvoie la réponse (parfois via le LB, parfois directement selon l'architecture)

**Trois rôles fondamentaux :**
1. **Distribution du trafic** — évite la surcharge d'un seul serveur
2. **Haute disponibilité** — redirige automatiquement vers les serveurs fonctionnels si l'un tombe
3. **Protection contre DoS/DDoS** — limitation du nombre de requêtes par seconde

**Ce qui est souvent mal compris :** Le Load Balancer n'est pas uniquement un outil de performance — c'est aussi un composant de sécurité. Sa capacité à limiter le débit de requêtes est une défense directe contre les attaques volumétriques.

---

### 2. Architecture typique avec Load Balancer

**Flux de l'architecture (Internet → Intranet) :**

```
Internet → External Firewall → Load Balancer → Internal Firewall → Intranet
                                      ↕
                                    DMZ (serveurs applicatifs)
```

**Rôle de chaque composant :**

| Composant | Rôle |
|-----------|------|
| **Internet** | Origine du trafic (HTTP/HTTPS, API, connexions applicatives, utilisateurs distants) |
| **External Firewall** | Premier filtre : bloque scans, ports interdits, trafic anormal. Ne laisse passer que connexions autorisées |
| **Load Balancer** | Distribution + Haute disponibilité + Protection volumétrique (DoS/DDoS) |
| **DMZ** | Zone démilitarisée hébergeant les serveurs exposés (web, applicatifs) |
| **Internal Firewall** | Bloque accès direct DMZ → Intranet, protège le réseau interne |
| **Intranet** | Réseau interne privé (postes, serveurs internes, BDD, ERP) — totalement isolé d'Internet |

⚠️ **Piège d'examen :** Le Load Balancer se positionne **entre** les deux firewalls, dans la zone DMZ ou juste avant. Il n'est pas un remplacement du pare-feu — les deux ont des rôles distincts et complémentaires.

---

### 3. Algorithmes d'équilibrage de charge

#### 3.1 Round-Robin

**Définition académique :** Algorithme d'équilibrage distribue les requêtes entrantes séquentiellement de façon cyclique entre tous les serveurs disponibles, indépendamment de leur charge actuelle.

**Mécanisme :** Requête 1 → Serveur 1, Requête 2 → Serveur 2, Requête 3 → Serveur 1, Requête 4 → Serveur 2...

**Cas d'usage :** Serveurs homogènes (même capacité), trafic uniforme.

**Limitation :** Ne tient pas compte de la charge réelle des serveurs. Un serveur déjà surchargé peut recevoir autant de requêtes qu'un serveur libre.

#### 3.2 Weighted Round-Robin (Round-Robin Pondéré)

**Définition académique :** Extension du Round-Robin classique où l'administrateur attribue un poids à chaque serveur proportionnellement à sa capacité de traitement. Les serveurs à poids plus élevé reçoivent une proportion plus grande des requêtes.

**Mécanisme :** Si Serveur 1 a poids 5 et Serveur 2 a poids 1 : parmi 6 requêtes, 5 vont à S1 et 1 va à S2.

**Cas d'usage :** Environnements hétérogènes où les serveurs n'ont pas la même puissance de calcul ni les mêmes ressources.

**Ce qui est mal compris :** Le poids est défini par l'**administrateur** (configuration manuelle), pas calculé automatiquement par le LB.

#### 3.3 Least Connections (Moins de connexions)

**Définition académique :** Algorithme d'équilibrage dynamique qui sélectionne à chaque nouvelle requête le serveur présentant le nombre le plus faible de connexions actives courantes, envoyant ainsi la requête au serveur actuellement le moins chargé.

**Mécanisme :**
- À chaque requête → le LB consulte l'état actuel de tous les serveurs
- Sélectionne celui avec le moins de connexions actives
- Exemple : S1 a 3 connexions, S2 a 2 → prochaine requête vers S2

**Cas d'usage :** Requêtes avec durées variables (sessions longues/courtes mélangées).

**Ce qui est mal compris :** Least Connections est **dynamique** — il adapte en temps réel. Le Round-Robin est **statique** — il tourne sans regarder la charge réelle.

#### 3.4 Random Connections (Connexions Aléatoires)

**Définition académique :** L'équilibreur de charge sélectionne aléatoirement deux serveurs parmi le cluster, puis applique l'algorithme Least Connections entre ces deux serveurs pour choisir celui qui reçoit la requête.

**Mécanisme :** Sélection aléatoire de 2 serveurs → Least Connections entre ces 2 → envoi au moins chargé des deux.

**Cas d'usage :** Grand volume de requêtes avec serveurs aux caractéristiques similaires (même disque, CPU, ressources).

**Ce qui est mal compris :** Random Connections n'est pas purement aléatoire — il y a une couche Least Connections en dessous.

#### 3.5 Least Time

**Définition :** Algorithme sélectionnant le serveur présentant le **temps de réponse le plus faible**, en mesurant la latence des serveurs.

#### 3.6 Hash

**Définition :** Distribue les requêtes selon une clé prédéfinie issue d'un algorithme de hachage (par exemple : adresse IP client, URL de la requête). Permet la persistance — le même client est toujours redirigé vers le même serveur.

#### 3.7 Source IP

**Définition :** Sélectionne le serveur sur la base de l'adresse IP source du client. Garantit que toutes les requêtes d'un même client IP sont traitées par le même serveur.

---

### 4. Session Affinity (Affinité de Session)

**Définition académique :** Mécanisme d'équilibrage de charge garantissant que toutes les requêtes d'un même utilisateur au cours d'une même session HTTP/HTTPS sont systématiquement redirigées vers le même serveur applicatif, en utilisant un cookie de session comme identifiant.

**Mécanisme étape par étape :**
1. Client envoie première requête → LB sélectionne un serveur, crée l'association cookie ↔ serveur en mémoire cache
2. Le serveur génère un cookie de session (dans la réponse HTTP)
3. Client renvoie ce cookie dans toutes les requêtes suivantes
4. Le LB lit le cookie dans l'en-tête de la requête → identifie le serveur associé → redirige vers ce serveur
5. La session reste sticky ("collante") pendant toute sa durée

**Pourquoi ça existe :** Certaines applications stockent l'état de session localement sur le serveur (panier e-commerce, session de jeu, workflow multi-étapes). Si une requête suivante va sur un autre serveur, la session est perdue.

**Implémentation technique :** Mise en cache en mémoire du LB pour mapper cookie ↔ serveur. Les en-têtes HTTP (requête et réponse) sont inspectés.

⚠️ **Piège d'examen :** L'affinité de session peut créer un déséquilibre de charge — si un utilisateur avec une longue session est toujours sur S1, S1 peut se retrouver surchargé alors que S2 est libre. C'est un trade-off entre cohérence de session et équilibrage optimal.

---

### 5. Répartition de charge via le Clustering

**Définition :** Architecture où plusieurs instances de Load Balancer travaillent ensemble pour assurer la haute disponibilité du service d'équilibrage lui-même (éviter que le LB soit un SPOF — Single Point of Failure).

**Composants du clustering LB :**

| Élément | Rôle |
|---------|------|
| **VIP (Virtual IP)** | Adresse IP virtuelle unique exposée aux clients (ex: 192.0.2.1) |
| **Active Load Balancer** | Instance principale qui traite le trafic en temps réel |
| **Passive Load Balancer** | Instance en veille, prend le relais si l'Active tombe |
| **Web Cluster** | Groupe de serveurs web finaux (ex: 192.168.0.1, .0.2, .0.3 sur port 80) |

**Fonctionnement :**
- Les clients contactent le VIP (adresse virtuelle unique)
- L'Active LB traite et distribue le trafic vers le Web Cluster
- Si l'Active LB tombe, le Passive LB prend le VIP et reprend le service
- Les serveurs web n'ont pas connaissance du failover LB

**4 bénéfices du clustering :**
1. **Haute disponibilité** — pas de SPOF au niveau du LB
2. **Répartition de charge** — entre serveurs du cluster
3. **Tolérance aux pannes** — failover automatique
4. **Scalabilité** — ajout de nœuds au cluster

---

### 6. Load Balancing Layers (Couches OSI)

**Load Balancing L4 (Transport) :**
- Décisions basées sur IP source/destination + port TCP/UDP
- Ne regarde pas le contenu applicatif
- Plus rapide, moins de ressources

**Load Balancing L7 (Application) :**
- Décisions basées sur le contenu HTTP (URL, cookies, headers, méthode HTTP)
- Permet routing basé sur le contenu (ex: /api → serveur API, /images → CDN)
- Session Affinity fonctionne à ce niveau (inspection des cookies)
- Plus intelligent mais plus consommateur en ressources

⚠️ **Piège d'examen :** Session Affinity nécessite un LB L7 (inspection cookies). Un LB L4 ne peut pas lire les cookies.

---

### 7. Outils de Load Balancing

| Outil | Caractéristiques clés |
|-------|----------------------|
| **EdgeNexus** | L4 à L7, accélération apps, SSO, gestion avancée trafic |
| **Azure Load Balancer** | Cloud Microsoft Azure |
| **AWS Elastic Load Balancing** | Cloud Amazon AWS |
| **NGINX Plus** | Open-source + commercial, L7 HTTP/HTTPS |
| **Citrix ADC Platforms** | Application Delivery Controller, entreprise |
| **Avi Vantage** | SDN-based, analytics intégrés |

---

## TABLEAUX DE COMPARAISON

### Tableau 1 : Algorithmes LB comparés

| Algorithme | Dynamique/Statique | Critère de sélection | Cas d'usage optimal |
|------------|-------------------|----------------------|---------------------|
| Round-Robin | Statique | Séquentiel | Serveurs homogènes |
| Weighted Round-Robin | Semi-statique (poids fixes) | Poids administrateur | Serveurs hétérogènes |
| Least Connections | Dynamique | Nb connexions actives | Sessions longues variables |
| Random Connections | Dynamique + Aléatoire | Aléatoire + Least Conn entre 2 | Grand volume, serveurs similaires |
| Least Time | Dynamique | Temps de réponse | Applications sensibles à la latence |
| Hash | Déterministe | Clé (IP, URL) | Persistance session, cache |
| Source IP | Déterministe | IP client | Persistance utilisateur |

### Tableau 2 : Session Affinity vs Hash vs Source IP

| Mécanisme | Identifiant utilisé | Niveau OSI | Stateful |
|-----------|--------------------|-----------|---------:|
| Session Affinity | Cookie HTTP | L7 | ✅ Oui |
| Hash (URL/IP) | Hash clé prédéfinie | L4-L7 | Partiellement |
| Source IP | IP source client | L4 | ✅ Oui (par IP) |

### Tableau 3 : L4 vs L7 Load Balancing

| Critère | L4 LB | L7 LB |
|---------|-------|-------|
| Données inspectées | IP + Port | Contenu HTTP (headers, cookies, URL) |
| Session Affinity | ❌ (pas de cookies) | ✅ Possible |
| Performance | Élevée (moins de traitement) | Moindre (inspection applicative) |
| Intelligence routing | Faible | Élevée |
| Exemple | HAProxy L4 mode | NGINX, Citrix ADC |

---

## QUESTIONS OUVERTES + RÉPONSES MODÈLES

### Q1 : Expliquez le rôle du Load Balancer dans la protection contre les attaques DoS/DDoS. Est-ce un mécanisme de sécurité ou uniquement de performance ?

**Réponse modèle :**
Le Load Balancer joue un double rôle, à la fois de performance et de sécurité. Du côté sécurité, il contrôle le nombre de requêtes par seconde reçues, ce qui lui permet d'absorber et de limiter les attaques volumétriques de type DoS (Denial of Service) ou DDoS (Distributed DoS). Ces attaques visent à saturer un serveur en l'inondant de requêtes pour le rendre indisponible.

En distribuant le trafic sur plusieurs serveurs, le Load Balancer dilue l'impact d'un pic volumétrique. En limitant le débit maximal accepté, il empêche la saturation des serveurs. Ce n'est donc pas uniquement un outil de performance — c'est un composant de défense en profondeur (defense-in-depth) dans une architecture de sécurité réseau. Il complète l'action du pare-feu externe (qui bloque les attaques évidentes) en gérant les attaques volumétriques qui passent à travers.

**Rubrique :**
- ✅ Plein points : double rôle (performance + sécurité) + mécanisme DoS/DDoS (limitation débit + distribution) + positionnement dans l'architecture
- ⚠️ Points partiels : sécurité mentionnée sans mécanisme précis
- ❌ Perte de points : affirmer que le LB est uniquement un outil de performance

---

### Q2 : Décrivez précisément le fonctionnement de l'algorithme Session Affinity (affinité de session). Dans quel contexte applicatif est-il indispensable ?

**Réponse modèle :**
L'affinité de session est un mécanisme d'équilibrage de charge opérant au niveau applicatif (couche 7). Son fonctionnement repose sur les cookies HTTP : lors de la première requête d'un utilisateur, le Load Balancer sélectionne un serveur et enregistre en cache mémoire l'association entre le cookie de session (présent dans les en-têtes HTTP) et ce serveur. Pour toutes les requêtes ultérieures de la même session, le LB lit le cookie dans l'en-tête de la requête, identifie le serveur associé et redirige systématiquement vers ce serveur.

Ce mécanisme est indispensable pour les applications stateful (avec état de session local) : applications e-commerce (panier d'achat), workflows multi-étapes, applications de jeu, ou tout service qui stocke l'état de session localement sur le serveur applicatif plutôt que dans une base de données partagée. Sans affinité de session, une requête suivante dirigée vers un autre serveur trouverait un contexte de session absent et l'utilisateur perdrait sa progression.

**Rubrique :**
- ✅ Plein points : mécanisme cookie + cache LB + en-têtes HTTP + contexte applicatif stateful + exemples concrets
- ⚠️ Points partiels : mécanisme correct mais sans explication du contexte applicatif qui le nécessite
- ❌ Perte de points : confondre Session Affinity avec Source IP (les deux maintiennent la persistance mais par mécanismes différents)

---

### Q3 : Comparez les algorithmes Round-Robin simple et Least Connections. Dans quels scénarios chacun est-il optimal ? Quelles sont leurs limites respectives ?

**Réponse modèle :**
Le Round-Robin simple distribue les requêtes séquentiellement de façon cyclique, sans tenir compte de l'état actuel des serveurs. Il est optimal lorsque les serveurs sont homogènes (même capacité) et que les requêtes ont des durées de traitement uniformes. Sa limite majeure est qu'il peut envoyer une requête à un serveur déjà surchargé ou traitant une longue transaction, simplement parce que c'est "son tour".

Least Connections est un algorithme dynamique qui consulte en temps réel le nombre de connexions actives sur chaque serveur et sélectionne celui qui en a le moins. Il est optimal lorsque les requêtes ont des durées de traitement variables (certaines courtes, d'autres longues), car il adapte la distribution à la charge réelle. Sa limite est un overhead de traitement plus élevé (le LB doit maintenir et consulter un état en temps réel pour chaque requête).

En synthèse : Round-Robin est simple, performant et adapté aux environnements homogènes et statiques. Least Connections est plus intelligent et équitable dans les environnements dynamiques avec charge variable, au prix d'une complexité accrue.

**Rubrique :**
- ✅ Plein points : mécanismes des deux + conditions optimales pour chacun + limites respectives
- ⚠️ Points partiels : mécanismes corrects sans analyse des limites
- ❌ Perte de points : affirmer que Least Connections est toujours supérieur

---

### Q4 : Expliquez l'architecture de clustering dans un système de Load Balancing. Quels problèmes résout-elle et quels bénéfices apporte-t-elle ?

**Réponse modèle :**
Le clustering dans un système de Load Balancing répond au problème du Single Point of Failure (SPOF) : si le Load Balancer lui-même tombe en panne, tout le service devient inaccessible malgré la disponibilité des serveurs backend.

L'architecture de clustering LB utilise un VIP (Virtual IP), adresse virtuelle unique exposée aux clients. En arrière-plan, un Active Load Balancer traite le trafic en temps réel et un Passive Load Balancer reste en veille. En cas de panne de l'Active, le Passive prend le VIP et reprend le service de manière transparente pour les clients.

Cette architecture apporte quatre bénéfices : haute disponibilité (pas de SPOF au niveau du LB), répartition de charge (entre serveurs du Web Cluster), tolérance aux pannes (failover automatique), et scalabilité (ajout de nœuds au cluster sans interruption de service).

**Rubrique :**
- ✅ Plein points : problème SPOF + VIP + Active/Passive + 4 bénéfices
- ⚠️ Points partiels : bénéfices listés sans explication du VIP et du mécanisme failover
- ❌ Perte de points : confondre le clustering LB avec le clustering des serveurs backend

---

## CROSS-LINKS INTER-CHAPITRES

- **Chapitre 7 (VPN)** : Le VPN Hardware intègre du load balancing natif. Le concentrateur VPN distribue les tunnels comme un LB distribue les connexions HTTP.
- **Chapitre Firewall** : Architecture typique : External Firewall → Load Balancer → Internal Firewall. Le LB est entre les deux couches de protection.
- **Chapitre IDS/IPS** : En mode inline, un IPS peut être positionné à côté ou intégré au LB. La session affinity peut compliquer l'inspection si différentes requêtes d'une même session passent par différents IPS.
- **Chapitre Proxy** : Le Reverse Proxy (comme NGINX) peut jouer le rôle de Load Balancer L7. La distinction est subtile : un Reverse Proxy ajoute des services (cache, compression, SSL termination), un LB pur se concentre sur la distribution.
