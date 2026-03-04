# CH2 QCM — Segmentation Réseau

---

## TIER 1: EASY (Direct Recall)

**Q1.** Quelle est la définition correcte de la segmentation réseau ?
- A) La pratique d'utiliser des VPN pour sécuriser les communications
- B) La pratique de diviser un réseau en segments plus petits et de séparer les groupes de systèmes ou d'applications
- C) L'utilisation de pare-feux pour filtrer le trafic entrant et sortant
- D) La mise en œuvre du chiffrement sur l'ensemble du réseau

**✅ Réponse : B**
- B est correct : c'est la définition textuelle exacte du cours.
- A : décrit une technologie VPN, non la segmentation.
- C : décrit la fonction d'un pare-feu.
- D : décrit le chiffrement, pas la segmentation.

---

**Q2.** Quel standard IEEE régit le marquage des VLANs ?
- A) IEEE 802.11
- B) IEEE 802.3
- C) IEEE 802.1Q
- D) IEEE 802.1X

**✅ Réponse : C**
- C : 802.1Q est le standard de trunk VLAN tagging.
- A : 802.11 = Wi-Fi.
- B : 802.3 = Ethernet filaire.
- D : 802.1X = authentification réseau (NAC), non VLAN.

---

**Q3.** Quels serveurs sont typiquement placés dans une DMZ ?
- A) Serveurs de bases de données, serveurs d'applications internes
- B) Serveurs Web, serveurs de messagerie, serveurs DNS
- C) Contrôleurs de domaine Active Directory, serveurs DHCP
- D) Serveurs NFS, serveurs de fichiers internes

**✅ Réponse : B**
- B : Ce sont les serveurs nécessitant une exposition Internet directe.
- A : DB et app servers sont internes — les exposer directement viole le principe de segmentation.
- C : AD et DHCP sont des services internes critiques qui ne doivent JAMAIS être en DMZ.
- D : NFS/fichiers internes n'ont pas besoin d'accès public.

---

**Q4.** Un hôte bastion dispose de combien d'interfaces réseau principales ?
- A) 1
- B) 2
- C) 3
- D) Autant que nécessaire selon le type

**✅ Réponse : B**
- B : Interface publique (Internet) + interface privée (intranet). C'est la définition de base.
- A : Possible pour un single-homed bastion, mais la définition standard est 2.
- C : 3 interfaces caractérisent un pare-feu three-legged DMZ, pas un bastion.
- D : Vrai pour les types avancés mais la réponse standard au niveau cours est 2.

---

**Q5.** Qu'est-ce qui caractérise le trafic Est-Ouest par rapport au trafic Nord-Sud ?
- A) Le trafic Est-Ouest est moins volumineux et mieux couvert par la sécurité traditionnelle
- B) Le trafic Est-Ouest désigne les communications entre clients externes et serveurs internes
- C) Le trafic Est-Ouest est plus volumineux et présente une surface d'attaque plus large que le Nord-Sud
- D) Le trafic Est-Ouest traverse toujours un pare-feu périmétrique

**✅ Réponse : C**
- C : Exactement selon le cours — plus volumineux, surface d'attaque plus large, historiquement négligé.
- A : C'est l'inverse — Est-Ouest est PLUS volumineux et MOINS couvert.
- B : Ça c'est la définition du trafic Nord-Sud (client externe ↔ datacenter).
- D : Le trafic Est-Ouest interne ne traverse pas forcément un FW périmétrique.

---

## TIER 2: MEDIUM (Scénario)

**Q6.** Une organisation possède un serveur web public, un serveur de bases de données interne et des postes de travail utilisateurs. Un architecte réseau souhaite appliquer la segmentation optimale. Quelle configuration est correcte ?
- A) Tous les serveurs dans la DMZ, postes de travail sur le réseau interne
- B) Serveur web en DMZ, serveur DB et postes de travail sur le réseau interne
- C) Serveur web et DB en DMZ, postes de travail sur réseau interne
- D) Tous les éléments sur le même réseau interne, protégés par un pare-feu périmétrique unique

**✅ Réponse : B**
- B : Le serveur web a besoin d'exposition Internet → DMZ. Le DB server contient des données sensibles et ne doit JAMAIS être exposé → réseau interne. Cette architecture préserve la DMZ pour les services publics uniquement.
- A : Mettre le DB en DMZ l'expose directement → violation flagrante du principe de segmentation.
- C : Idem — le DB en DMZ est inacceptable.
- D : Réseau plat — vulnérable à la propagation d'attaques si le serveur web est compromis.

---

**Q7.** Dans une architecture DMZ à double pare-feu, quel pare-feu est compromis en premier si un attaquant pénètre depuis Internet ?
- A) Le pare-feu interne, car il est plus exposé aux requêtes HTTP
- B) Le pare-feu externe (public firewall), car il est directement exposé à Internet
- C) Les deux simultanément, car ils partagent la même infrastructure
- D) Aucun — dans une dual-FW DMZ, les attaquants ne peuvent pas pénétrer

**✅ Réponse : B**
- B : Internet → Pare-feu externe → DMZ → Pare-feu interne → Réseau interne. Le pare-feu externe est la première ligne de défense.
- A : Le pare-feu interne est protégé derrière la DMZ et le pare-feu externe.
- C : Dans une dual-FW, les deux pare-feux sont des dispositifs séparés — pas de partage d'infrastructure critique.
- D : Aucune architecture ne garantit une imperméabilité totale.

---

**Q8.** Une entreprise veut implémenter la segmentation réseau sans acheter de nouveau matériel. Quelle approche est la plus appropriée ?
- A) Segmentation physique avec nouveau câblage dédié
- B) Segmentation logique par VLANs sur les commutateurs existants
- C) Déploiement d'un nouveau pare-feu matériel pour chaque segment
- D) Migration vers un réseau plat avec des règles ACL strictes

**✅ Réponse : B**
- B : La segmentation logique VLAN exploite l'infrastructure matérielle existante — c'est son avantage clé explicitement mentionné dans le cours.
- A : Requiert du nouveau matériel et câblage — contraire à l'objectif.
- C : Chaque pare-feu dédié coûte cher et nécessite du matériel supplémentaire.
- D : Un réseau plat avec ACL n'est pas une segmentation — c'est exactement le problème qu'on cherche à éviter.

---

**Q9.** Pourquoi les comptes utilisateurs sont-ils interdits sur un hôte bastion ?
- A) Pour réduire la charge CPU du système
- B) Parce que les bastions ne supportent pas les protocoles d'authentification
- C) Un compte utilisateur permettrait à une personne de se connecter, prendre le contrôle de la machine et l'utiliser comme pivot vers Internet/intranet
- D) Pour respecter les exigences de conformité GDPR

**✅ Réponse : C**
- C : Raison de sécurité explicite dans le cours — un compte local crée un vecteur d'attaque permettant la prise de contrôle du bastion.
- A : La CPU n'est pas la préoccupation principale ici.
- B : Faux — les bastions supportent l'authentification mais n'autorisent pas les logins locaux par politique.
- D : GDPR n'est pas mentionné dans ce contexte ; la raison est purement sécuritaire.

---

**Q10.** Le modèle Zero Trust est mis en œuvre dans une organisation. Un employé interne tente d'accéder à une ressource réseau depuis son poste habituel. Que se passe-t-il ?
- A) L'accès est automatiquement accordé car l'employé est interne et de confiance
- B) L'accès est refusé par défaut car le Zero Trust bloque tout trafic interne
- C) La demande d'accès est vérifiée et authentifiée avant d'être accordée, quelle que soit l'origine
- D) L'accès est accordé uniquement si l'employé se trouve physiquement dans les locaux

**✅ Réponse : C**
- C : Zero Trust — "never trust, always verify" — même pour les utilisateurs internes. Le Control Plane vérifie chaque demande.
- A : C'est le modèle traditionnel périmétrique, pas Zero Trust.
- B : Zero Trust ne bloque pas tout — il vérifie tout avant d'accorder ou refuser.
- D : La localisation physique n'est pas le seul critère — Zero Trust évalue rôle, type d'appareil, heure, etc.

---

**Q11.** Dans une architecture de segmentation réseau avec DMZ, quelle règle de trafic est INCORRECTE ?
- A) Internet → DMZ : autorisé sur ports 80, 443, 25
- B) DMZ → Réseau interne : autorisé bidirectionnellement
- C) Réseau interne → DMZ : unidirectionnel ou bidirectionnel selon les besoins
- D) Internet → Réseau interne : bloqué

**✅ Réponse : B**
- B est INCORRECT : Par défaut, les hôtes de la DMZ NE PEUVENT PAS se connecter au réseau interne. Le trafic bidirectionnel entre réseau interne et DMZ est l'exception (pour backup/AD auth), pas la règle.
- A : Correct — ports standard pour services web et mail.
- C : Correct — le trafic interne vers DMZ est typiquement autorisé.
- D : Correct — Internet ne peut jamais accéder directement au réseau interne.

---

**Q12.** Quelle est la différence fondamentale entre la virtualisation réseau et la segmentation VLAN ?
- A) Les VLANs sont plus sécurisés que la virtualisation réseau
- B) La virtualisation réseau peut créer des réseaux virtuels complets avec des espaces d'adressage IP identiques sur la même infrastructure physique, ce que les VLANs ne permettent pas
- C) Les VLANs opèrent à la couche 3, la virtualisation réseau à la couche 2
- D) La virtualisation réseau nécessite obligatoirement du matériel dédié

**✅ Réponse : B**
- B : Différence clé — la virtualisation réseau permet à deux réseaux virtuels d'avoir des adresses IP identiques (par ex. 192.168.1.0/24) sans conflit car ils sont complètement isolés par la couche de virtualisation. Les VLANs ne permettent pas ça.
- A : Pas de hiérarchie absolue — les deux ont des vulnérabilités propres.
- C : C'est l'inverse — VLANs = L2 ; virtualisation réseau = L2 à L7.
- D : La virtualisation réseau utilise l'infrastructure physique existante.

---

## TIER 3: HARD (Pièges professeur)

**Q13.** Un administrateur déclare : "Notre réseau est sécurisé parce que nous avons un pare-feu périmétrique et que tout le trafic Internet est filtré." Quelle vulnérabilité critique cette affirmation ignore-t-elle ?
- A) Le pare-feu ne supporte pas le protocole IPv6
- B) En l'absence de segmentation interne, un attaquant ayant franchi le périmètre peut se déplacer latéralement sans restriction vers toutes les ressources internes
- C) Le pare-feu périmétrique est un point de défaillance unique
- D) Le filtrage du trafic Internet ralentit les performances réseau

**✅ Réponse : B**
- B : C'est exactement le problème du réseau plat décrit dans le cours. La sécurité périmétrique seule est insuffisante — le mouvement latéral post-breach est la menace principale que la segmentation adresse.
- A : IPv6 est une préoccupation légitime mais secondaire par rapport au problème fondamental soulevé.
- C : Le SPOF est un problème réel mais ne répond pas à la vulnérabilité principale de l'affirmation (absence de segmentation interne).
- D : Les performances ne sont pas la vulnérabilité critique ici.

---

**Q14.** Un ingénieur réseau configure un bastion double interface (dual-homed) et active le routage IP entre ses deux interfaces pour "faciliter la communication". Quel est le problème ?
- A) Aucun — activer le routage améliore les performances
- B) Le routage activé transforme le bastion en routeur standard, éliminant son rôle d'intermédiaire contrôlé et permettant le trafic direct entre les deux réseaux
- C) Le routage IP n'est pas supporté sur les hôtes bastions
- D) Activer le routage nécessite une licence supplémentaire

**✅ Réponse : B**
- B : Un Non-routing Dual-homed Host a précisément pour propriété critique que le routage soit DÉSACTIVÉ entre ses interfaces. Si on active le routage, il se comporte comme un simple routeur — la séparation de sécurité est détruite. Le cours stipule explicitement que ce type "ne route pas entre ses connexions réseau."
- A : Complètement faux — activer le routage détruit la sécurité du dispositif.
- C : Techniquement faux — les bastions sont des OS standard (Linux, BSD) qui supportent IP forwarding.
- D : Distractor sans fondement.

---

**Q15.** Dans le contexte du Zero Trust, quelle affirmation est VRAIE ?
- A) Le Zero Trust remplace les pare-feux et les DMZ dans les architectures modernes
- B) Le Zero Trust protège le réseau en renforçant ses périmètres
- C) Le Zero Trust protège les accès aux ressources, non le réseau lui-même, et s'applique à tous les utilisateurs y compris les employés internes
- D) Le Zero Trust est uniquement applicable aux utilisateurs distants accédant via VPN

**✅ Réponse : C**
- C : Formulation exacte du cours — "Le Zero Trust ne protège pas le réseau, il protège les accès." S'applique à TOUS les utilisateurs, internes compris.
- A : Zero Trust COMPLÈTE les pare-feux et DMZ — il ne les remplace pas. Les technologies peuvent coexister.
- B : C'est la définition de la sécurité périmétrique traditionnelle, pas du Zero Trust.
- D : Zero Trust s'applique à tous les accès, pas seulement aux accès distants.

---

**Q16.** Quelle affirmation sur la segmentation physique est CORRECTE ?
- A) La segmentation physique est moins sécurisée que la segmentation logique par VLANs
- B) La segmentation physique est considérée comme l'une des méthodes les plus sécurisées d'isolation réseau, malgré ses coûts élevés
- C) La segmentation physique ne nécessite pas de pare-feux dédiés par segment
- D) La segmentation physique peut être mise en œuvre sans matériel supplémentaire si les commutateurs existants supportent la fonction

**✅ Réponse : B**
- B : Textuellement confirmé dans le cours — "la segmentation physique est considérée comme l'une des méthodes les plus sécurisées d'isolation réseau." Le coût est un inconvénient, pas une réfutation de sa sécurité.
- A : C'est l'inverse — physique > logique en termes de sécurité d'isolation.
- C : Faux — le cours mentionne explicitement que "chaque segment doit disposer de ses propres connexions réseau, câblage physique et pare-feu dédiés."
- D : Si on peut le faire sans matériel supplémentaire, c'est de la segmentation logique, pas physique par définition.

---

**Q17.** Un analyste sécurité observe une forte augmentation du trafic entre les serveurs d'application et les serveurs de bases de données dans un même datacenter. Quel type de trafic est-ce et quelle est la meilleure contre-mesure ?
- A) Trafic Nord-Sud ; déployer un pare-feu périmétrique plus puissant
- B) Trafic Est-Ouest ; implémenter la micro-segmentation et l'approche SDN pour surveiller et contrôler ce trafic intra-datacenter
- C) Trafic Nord-Sud ; utiliser un VPN entre les serveurs
- D) Trafic Est-Ouest ; désactiver les services réseau pour réduire le volume

**✅ Réponse : B**
- B : Serveur-à-serveur dans un datacenter = Est-Ouest par définition. La micro-segmentation + SDN sont les contre-mesures explicitement recommandées dans le cours pour ce type de trafic.
- A : Nord-Sud = client externe ↔ datacenter. Un FW périmétrique ne surveille pas le trafic interne.
- C : Nord-Sud mal identifié + VPN entre serveurs internes n'est pas la solution standard pour ce problème.
- D : Désactiver des services réseau casserait les applications — ce n'est pas une contre-mesure.

---

**Q18.** Dans quelle situation l'utilisation d'une "victim machine" est-elle justifiée selon le cours ?
- A) Pour héberger les services les plus critiques nécessitant une haute disponibilité
- B) Pour tester des applications dont les vulnérabilités de sécurité sont encore inconnues, ou pour exécuter des services non sécurisés, sachant que la machine est jetable et non réutilisable
- C) Pour remplacer le bastion principal en cas de panne
- D) Pour stocker les journaux d'audit du réseau interne

**✅ Réponse : B**
- B : Définition exacte du cours. "Services non sécurisés ou nouvelles applications dont les failles sont inconnues." Point critique : "ces machines ne sont pas réutilisables."
- A : C'est l'exact opposé — les services critiques ne vont PAS sur une victim machine.
- C : Une victim machine n'est pas un bastion de secours — c'est une machine sacrifiée intentionnellement. Si compromise, elle est abandonnée.
- D : Les journaux d'audit vont sur un serveur dédié séparé, pas sur une victim machine.
