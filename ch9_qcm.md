# CH9 — LOAD BALANCER : BANQUE QCM (3 NIVEAUX)

---

## NIVEAU FACILE — 5 Questions (Rappel direct)

---

**Q1. Quel est le rôle principal d'un Load Balancer en matière de sécurité réseau ?**

A) Chiffrer le trafic entre Internet et les serveurs  
B) Filtrer les paquets selon des règles ACL  
C) Contrôler le nombre de requêtes pour se protéger contre les attaques DoS/DDoS  
D) Authentifier les utilisateurs avant l'accès aux serveurs  

✅ **Réponse : C**

**Pourquoi C :** Le Load Balancer contrôle le nombre de requêtes par seconde, ce qui lui permet de se protéger contre les attaques basées sur le débit comme DoS (Denial of Service) et DDoS (Distributed DoS) en empêchant la saturation des serveurs.

**Pourquoi pas A :** Le chiffrement est rôle du VPN ou des protocoles SSL/TLS, pas du Load Balancer.

**Pourquoi pas B :** Le filtrage ACL est une fonction du pare-feu (Firewall), pas du Load Balancer.

**Pourquoi pas D :** L'authentification est gérée par des serveurs dédiés (RADIUS, LDAP, IAM), non par le LB.

---

**Q2. Dans un algorithme Round-Robin simple, comment sont distribuées les requêtes ?**

A) En fonction du nombre de connexions actives de chaque serveur  
B) Séquentiellement et cycliquement entre tous les serveurs disponibles  
C) Aléatoirement en choisissant un serveur différent à chaque fois  
D) En fonction du poids attribué à chaque serveur par l'administrateur  

✅ **Réponse : B**

**Pourquoi B :** Round-Robin distribue les requêtes de façon séquentielle : S1, S2, S1, S2... de manière cyclique, indépendamment de la charge réelle des serveurs.

**Pourquoi pas A :** C'est la définition de Least Connections.

**Pourquoi pas C :** C'est la définition de Random Connections (avec une couche Least Connections en plus).

**Pourquoi pas D :** C'est la définition de Weighted Round-Robin.

---

**Q3. Quel composant d'une architecture de clustering Load Balancer est l'adresse IP unique exposée aux clients ?**

A) Internal IP  
B) Gateway IP  
C) VIP (Virtual IP)  
D) NAS IP  

✅ **Réponse : C**

**Pourquoi C :** Le VIP (Virtual IP) est l'adresse IP virtuelle unique que voient les clients. Elle reste constante même en cas de failover entre l'Active et le Passive Load Balancer.

**Pourquoi pas A :** L'Internal IP est l'adresse de communication interne entre les composants du cluster LB.

**Pourquoi pas B :** La Gateway IP désigne la passerelle réseau, concept distinct du VIP.

**Pourquoi pas D :** NAS est le Network Access Server du chapitre VPN — rien à voir avec le clustering LB.

---

**Q4. Quel algorithme d'équilibrage de charge est le plus adapté lorsque les serveurs ont des capacités matérielles différentes ?**

A) Round-Robin  
B) Weighted Round-Robin  
C) Random Connections  
D) Least Time  

✅ **Réponse : B**

**Pourquoi B :** Weighted Round-Robin a été développé précisément pour s'adapter aux environnements hétérogènes. L'administrateur attribue des poids proportionnels à la capacité de chaque serveur, les plus puissants recevant une part proportionnellement plus grande du trafic.

**Pourquoi pas A :** Round-Robin simple traite tous les serveurs équitablement — inadapté si les capacités diffèrent.

**Pourquoi pas C :** Random Connections est adapté aux serveurs aux caractéristiques similaires.

**Pourquoi pas D :** Least Time sélectionne selon la latence, pas selon la capacité matérielle déclarée.

---

**Q5. Quelle est la caractéristique principale de l'algorithme Session Affinity ?**

A) Il sélectionne aléatoirement le serveur à chaque requête  
B) Il suit le cookie de session pour rediriger toutes les requêtes d'un client vers le même serveur  
C) Il distribue les requêtes selon l'adresse IP source du client  
D) Il choisit le serveur ayant le moins de connexions actives  

✅ **Réponse : B**

**Pourquoi B :** L'affinité de session utilise le cookie de session présent dans les en-têtes HTTP pour identifier l'utilisateur et rediriger systématiquement toutes ses requêtes vers le même serveur applicatif, garantissant la continuité de la session.

**Pourquoi pas A :** Le caractère aléatoire est l'opposé de l'objectif de Session Affinity.

**Pourquoi pas C :** La redistribution par IP source est la définition de l'algorithme Source IP, qui est différent de Session Affinity (mécanisme différent même si l'effet de persistance est similaire).

**Pourquoi pas D :** C'est la définition de Least Connections.

---

## NIVEAU MOYEN — 7 Questions (Compréhension mécanisme)

---

**Q6. Un administrateur configure un Load Balancer avec l'algorithme Least Connections. Serveur 1 a 5 connexions actives, Serveur 2 a 3 connexions actives, Serveur 3 a 7 connexions actives. Quelle nouvelle requête entrante est dirigée vers quel serveur ?**

A) Serveur 1 (premier dans la liste)  
B) Serveur 2 (le moins chargé)  
C) Serveur 3 (distribution inverse)  
D) Distribution aléatoire entre les trois  

✅ **Réponse : B**

**Pourquoi B :** Least Connections sélectionne le serveur avec le plus **petit** nombre de connexions actives. Serveur 2 avec 3 connexions actives est le moins chargé des trois (S1=5, S3=7), donc la prochaine requête lui est envoyée.

**Pourquoi pas A :** Le critère n'est pas l'ordre dans la liste mais la charge actuelle.

**Pourquoi pas C :** "Distribution inverse" n'est pas un algorithme défini.

**Pourquoi pas D :** Least Connections est déterministe (pas aléatoire) — il choisit toujours le moins chargé.

---

**Q7. Pourquoi la Session Affinity peut-elle créer un problème d'équilibrage de charge dans certains scénarios ?**

A) Parce qu'elle consomme trop de bande passante réseau  
B) Parce qu'un serveur peut se retrouver surchargé si de nombreuses sessions longues lui sont assignées  
C) Parce qu'elle n'est compatible qu'avec l'algorithme Round-Robin  
D) Parce qu'elle empêche le failover en cas de panne d'un serveur  

✅ **Réponse : B**

**Pourquoi B :** L'affinité de session fixe un utilisateur sur un serveur pour toute la durée de sa session. Si plusieurs utilisateurs avec de longues sessions se retrouvent tous sur le Serveur 1, celui-ci sera surchargé pendant que les autres serveurs restent sous-utilisés. C'est le trade-off entre cohérence de session et équilibrage optimal.

**Pourquoi pas A :** La Session Affinity n'a pas d'impact direct sur la bande passante.

**Pourquoi pas C :** Elle est un mécanisme indépendant de l'algorithme de base.

**Pourquoi pas D :** En cas de panne du serveur, la session est perdue et le LB doit réassigner — mais ce n'est pas un problème d'équilibrage, c'est un problème de disponibilité.

---

**Q8. Dans quelle position de l'architecture réseau se trouve typiquement le Load Balancer ?**

A) Devant le pare-feu externe, exposé directement à Internet  
B) Entre le pare-feu externe et le pare-feu interne, dans la DMZ  
C) Derrière le pare-feu interne, dans l'Intranet  
D) À la place du pare-feu externe, avec des fonctions de filtrage combinées  

✅ **Réponse : B**

**Pourquoi B :** L'architecture standard positionne le LB entre les deux niveaux de protection : External Firewall → Load Balancer → Internal Firewall. Le LB est dans la zone DMZ ou juste en amont, après le premier filtrage du pare-feu externe.

**Pourquoi pas A :** Le LB ne doit pas être exposé directement à Internet sans protection — le pare-feu externe filtre d'abord.

**Pourquoi pas C :** Le LB traite le trafic venant d'Internet, il est logiquement avant le réseau interne.

**Pourquoi pas D :** Le LB ne remplace pas le pare-feu — ce sont deux composants aux fonctions distinctes et complémentaires. ⚠️ Piège classique d'examen.

---

**Q9. Quelle différence fondamentale existe entre l'algorithme Hash (basé sur l'IP client) et l'algorithme Source IP ?**

A) Hash utilise l'adresse MAC, Source IP utilise l'adresse IP  
B) Hash applique une fonction de hachage sur une clé (IP ou URL), Source IP se base directement sur l'IP source  
C) Source IP est dynamique, Hash est statique  
D) Il n'y a aucune différence — ce sont deux noms pour le même algorithme  

✅ **Réponse : B**

**Pourquoi B :** L'algorithme Hash applique une fonction de hachage sur une clé prédéfinie (qui peut être l'IP source, l'URL, ou une combinaison) pour déterminer le serveur. L'algorithme Source IP se base directement sur l'adresse IP source sans opération de hachage intermédiaire. La clé de Hash peut aussi être l'URL — ce que Source IP ne peut pas faire.

**Pourquoi pas A :** Ni l'un ni l'autre n'utilise l'adresse MAC au niveau du Load Balancer.

**Pourquoi pas C :** Les deux sont déterministes (même entrée → même sortie), ni l'un ni l'autre n'est "dynamique" au sens de Least Connections.

**Pourquoi pas D :** Ce sont des algorithmes distincts avec des mécanismes différents, même si l'effet de persistance peut être similaire.

---

**Q10. Un site e-commerce traite des commandes en plusieurs étapes (panier → paiement → confirmation). Quel algorithme de Load Balancing est indispensable pour ce cas d'usage ?**

A) Round-Robin — pour une distribution équitable  
B) Least Connections — pour éviter la surcharge  
C) Session Affinity — pour maintenir le contexte de session  
D) Random Connections — pour distribuer le trafic sur le cluster  

✅ **Réponse : C**

**Pourquoi C :** Un workflow e-commerce multi-étapes est une application stateful — l'état du panier et du paiement est maintenu en session sur le serveur. Si une étape est traitée par S1 et la suivante par S2, S2 n'a pas connaissance de la session du client → perte de données. Session Affinity garantit que toutes les étapes du processus sont traitées par le même serveur.

**Pourquoi pas A :** Round-Robin distribuerait les différentes étapes sur des serveurs différents → sessions perdues.

**Pourquoi pas B :** Least Connections distribuerait aussi les étapes sur des serveurs différents selon la charge.

**Pourquoi pas D :** Random Connections n'assure pas la persistance de session.

---

**Q11. Quelle est la différence entre un Load Balancer L4 et L7 en termes de capacités de routage ?**

A) L4 opère sur les URLs et cookies, L7 opère sur IP et ports uniquement  
B) L4 opère sur IP et ports (transport), L7 inspecte le contenu HTTP (cookies, URL, headers)  
C) L4 et L7 ont les mêmes capacités mais à des vitesses différentes  
D) L7 est moins sécurisé car il inspecte le contenu des paquets  

✅ **Réponse : B**

**Pourquoi B :** Un LB L4 (couche transport) prend ses décisions uniquement sur la base de l'IP source/destination et du port TCP/UDP. Un LB L7 (couche application) peut inspecter le contenu HTTP — cookies, URL, en-têtes — et prendre des décisions de routage plus granulaires. Session Affinity nécessite impérativement un LB L7.

**Pourquoi pas A :** C'est l'inverse des définitions.

**Pourquoi pas C :** La différence n'est pas que la vitesse — ce sont des capacités fonctionnellement différentes.

**Pourquoi pas D :** L'inspection du contenu à L7 est une fonctionnalité avancée, pas une vulnérabilité.

---

**Q12. Dans un cluster de Load Balancing avec un Active et un Passive Load Balancer, que se passe-t-il en cas de panne de l'Active LB du point de vue des clients ?**

A) Les clients voient une erreur et doivent se reconnecter manuellement  
B) Le Passive LB prend le VIP et continue à servir le trafic de manière transparente  
C) L'adresse IP virtuelle est retirée et les clients doivent utiliser une autre IP  
D) Les serveurs web du cluster continuent à répondre directement sans passer par le LB  

✅ **Réponse : B**

**Pourquoi B :** Le failover du clustering LB est transparent pour les clients. Le Passive LB prend le VIP (Virtual IP) — la même adresse IP que les clients utilisaient — et reprend le service. Du point de vue client, rien ne change (l'IP de destination reste identique).

**Pourquoi pas A :** Le failover automatique est précisément conçu pour éviter cette interruption.

**Pourquoi pas C :** Le VIP ne disparaît pas — il est repris par le Passive LB.

**Pourquoi pas D :** Les serveurs web ne connaissent pas l'architecture LB et ne peuvent pas accepter directement des connexions clients sans reconfiguration.

---

## NIVEAU DIFFICILE — 6 Questions (Pièges professeurs)

---

**Q13. Quelle affirmation concernant l'algorithme Random Connections est CORRECTE ?**

A) Il distribue les requêtes purement aléatoirement entre tous les serveurs du cluster  
B) Il sélectionne aléatoirement deux serveurs puis envoie la requête à celui ayant le moins de connexions actives  
C) Il sélectionne aléatoirement un serveur unique pour chaque requête  
D) Il combine Round-Robin aléatoire avec un poids dynamique calculé par le LB  

✅ **Réponse : B**

**Pourquoi B :** Random Connections opère en deux étapes : (1) sélection aléatoire de **deux** serveurs parmi le cluster, (2) application de Least Connections entre ces deux serveurs pour choisir le moins chargé. Ce n'est donc pas purement aléatoire — il y a une optimisation Least Connections dans la sélection finale.

**Pourquoi pas A :** "Purement aléatoire" serait incorrect — la sélection finale entre les 2 serveurs est déterministe (Least Connections).

**Pourquoi pas C :** La sélection porte sur 2 serveurs, pas 1 uniquement.

**Pourquoi pas D :** Pas de poids dynamique — c'est Weighted Round-Robin qui utilise des poids.

---

**Q14. Un étudiant affirme : "Le pare-feu interne dans l'architecture Load Balancer sert à protéger les serveurs de la DMZ contre Internet." Cette affirmation est :**

A) Correcte — le pare-feu interne filtre le trafic entrant depuis Internet  
B) Incorrecte — le pare-feu interne protège l'Intranet contre la DMZ, pas contre Internet  
C) Correcte — les deux pare-feux ont la même fonction  
D) Incorrecte — il n'y a pas de pare-feu interne dans une architecture Load Balancer standard  

✅ **Réponse : B**

**Pourquoi B :** L'affirmation est incorrecte. Le pare-feu **externe** protège l'infrastructure contre Internet. Le pare-feu **interne** protège le réseau interne (Intranet) contre la DMZ — il bloque l'accès direct depuis les serveurs DMZ vers l'Intranet, empêche qu'un serveur compromis en DMZ n'accède aux ressources internes sensibles, et n'autorise que des communications strictement définies.

**Pourquoi pas A :** Le pare-feu interne n'est pas positionné entre Internet et la DMZ.

**Pourquoi pas C :** Les deux pare-feux ont des rôles distincts dans deux directions de protection différentes.

**Pourquoi pas D :** Le pare-feu interne est un composant standard et documenté de l'architecture à deux niveaux de protection.

---

**Q15. Pour quelle raison l'algorithme Weighted Round-Robin est-il qualifié de "semi-statique" par opposition à Least Connections qui est "dynamique" ?**

A) Parce que les poids dans Weighted RR sont calculés automatiquement par le LB en temps réel  
B) Parce que les poids dans Weighted RR sont définis manuellement par l'administrateur et ne s'adaptent pas à la charge réelle  
C) Parce que Weighted RR ne fonctionne qu'avec un nombre fixe de serveurs  
D) Parce que Least Connections utilise des poids, alors que Weighted RR n'en utilise pas  

✅ **Réponse : B**

**Pourquoi B :** Dans Weighted Round-Robin, l'administrateur définit manuellement les poids selon la capacité estimée de chaque serveur. Ces poids ne s'adaptent pas à la charge réelle en cours d'exécution — si S1 est momentanément saturé mais que son poids est 5, il continuera à recevoir 5x plus de requêtes que S2. Il est "semi-statique" car les poids peuvent être modifiés par l'admin, mais pas automatiquement. Least Connections est "dynamique" car il consulte l'état réel à chaque requête.

**Pourquoi pas A :** L'inverse — les poids sont manuels, jamais calculés automatiquement.

**Pourquoi pas C :** Aucune limitation sur le nombre de serveurs.

**Pourquoi pas D :** C'est l'inverse — Weighted RR utilise des poids, Least Connections utilise le nb de connexions actives.

---

**Q16. Dans quel cas l'algorithme Least Connections peut-il paradoxalement sous-performer par rapport à Round-Robin ?**

A) Quand tous les serveurs ont le même nombre de connexions actives en permanence  
B) Quand les requêtes sont très courtes et homogènes, avec de nombreuses connexions qui s'ouvrent et se ferment rapidement  
C) Quand les serveurs ont des capacités différentes  
D) Quand la session affinity est activée simultanément  

✅ **Réponse : B**

**Pourquoi B :** Si les requêtes sont très courtes et que les connexions s'ouvrent et se ferment quasi-instantanément, l'overhead de consultation de l'état en temps réel pour chaque requête dans Least Connections devient un coût non négligeable. De plus, dans ce scénario, les connexions actives à tout instant tendent à être quasi-égales sur tous les serveurs — Least Connections ne fait alors que Round-Robin avec un overhead supplémentaire. Round-Robin simple serait plus efficace.

**Pourquoi pas A :** Si tous ont le même nombre de connexions, Least Connections a le même comportement que Round-Robin — performance identique, pas "sous-performance".

**Pourquoi pas C :** Least Connections gère bien les serveurs à capacités différentes (il sélectionne naturellement les moins chargés).

**Pourquoi pas D :** Session Affinity et Least Connections sont des mécanismes indépendants — leur combinaison ne dégrade pas intrinsèquement Least Connections.

---

**Q17. Un attaquant tente un DDoS contre un serveur web protégé par un Load Balancer. L'attaquant envoie des millions de requêtes depuis des milliers d'IPs différentes. Quelle limitation du Load Balancer comme défense anti-DDoS cette attaque exploite-t-elle ?**

A) Le Load Balancer ne peut pas distinguer le trafic légitime du trafic malveillant au niveau applicatif  
B) L'algorithme Session Affinity permet à l'attaquant de cibler un serveur spécifique  
C) Le Load Balancer distribue l'attaque sur tous les serveurs, surchargeant l'ensemble du cluster  
D) Le Load Balancer ne peut pas filtrer par IP source quand les IPs sont trop nombreuses  

✅ **Réponse : C**

**Pourquoi C :** C'est la limite fondamentale du LB face à un DDoS distribué. Le LB distribue le trafic — légitime ou non — entre tous les serveurs. Si le volume d'attaque dépasse la capacité totale du cluster, tous les serveurs sont surchargés en même temps. Le LB ne "bloque" pas le trafic malveillant, il le répartit. Une défense anti-DDoS robuste nécessite des outils spécialisés (scrubbing centers, CDN avec protection DDoS, WAF).

**Pourquoi pas A :** C'est aussi vrai, mais la question demande la limitation spécifiquement liée à l'attaque décrite (millions d'IPs différentes → volumétrique sur l'ensemble du cluster).

**Pourquoi pas B :** Session Affinity basée sur cookie ne permet pas à l'attaquant de cibler un serveur spécifique dans ce scénario.

**Pourquoi pas D :** C'est aussi une vraie limitation, mais la conséquence principale est C.

---

**Q18. Quelle propriété du VIP (Virtual IP) dans un cluster Load Balancer est critique pour la transparence du failover vis-à-vis des clients ?**

A) Le VIP change à chaque requête pour assurer la rotation des serveurs  
B) Le VIP reste identique que ce soit l'Active ou le Passive LB qui répond  
C) Le VIP est visible uniquement par les serveurs du Web Cluster, pas par les clients  
D) Le VIP utilise un algorithme de hachage pour sélectionner le LB actif  

✅ **Réponse : B**

**Pourquoi B :** La transparence du failover repose sur le fait que le VIP ne change pas lors du basculement Active → Passive. Les clients ont une connexion vers une IP qui reste stable (le VIP). Quand l'Active tombe, le Passive prend le VIP — même adresse, même connexion apparente pour les clients. C'est la propriété fondamentale qui rend le failover transparent.

**Pourquoi pas A :** Un VIP qui change à chaque requête n'est pas un VIP — c'est juste un DNS round-robin, mécanisme différent.

**Pourquoi pas C :** Le VIP est précisément l'adresse exposée aux clients (c'est son rôle principal).

**Pourquoi pas D :** Aucun algorithme de hachage ne sélectionne le LB actif — c'est le protocole de heartbeat/failover qui détermine qui est actif.
