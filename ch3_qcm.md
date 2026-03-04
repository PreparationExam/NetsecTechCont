# CH3 QCM — Firewalls

---

## TIER 1: EASY (Direct Recall)

**Q1.** À quelle couche du modèle OSI opère le filtrage de paquets ?
- A) Couche 7 (Application)
- B) Couche 5 (Session)
- C) Couche 3 (Réseau)
- D) Couche 2 (Liaison de données)

**✅ Réponse : C**
- C : Le filtrage de paquets opère au niveau réseau (IP layer). Confirmé explicitement dans le cours.
- A : C'est la couche des passerelles applicatives et proxies.
- B : C'est la couche des passerelles de niveau circuit.
- D : La couche 2 supporte le filtrage MAC (ACL L2) mais le filtrage de paquets est principalement L3.

---

**Q2.** Quel type de pare-feu maintient une table d'états pour suivre les sessions établies ?
- A) Filtrage de paquets sans état
- B) Passerelle de niveau circuit
- C) Inspection multi-couches avec état (stateful)
- D) ACL standard

**✅ Réponse : C**
- C : La "state table" est la caractéristique définissante de l'inspection stateful.
- A : Sans état = stateless = PAS de state table.
- B : La passerelle de niveau circuit suit les sessions TCP au niveau handshake mais ne maintient pas une state table multi-couches.
- D : Les ACL sont des règles de filtrage, pas un mécanisme de suivi d'état.

---

**Q3.** Quels exemples sont des pare-feux matériels ?
- A) Norton, McAfee, Kaspersky
- B) iptables, UFW, Windows Firewall
- C) Cisco ASA, FortiGate
- D) pfSense, Smoothwall, Cisco SonicWall

**✅ Réponse : C**
- C : Cisco ASA et FortiGate sont les exemples explicitement cités dans le cours comme firewalls matériels.
- A : Norton/McAfee/Kaspersky = firewalls logiciels.
- B : iptables/UFW/Windows FW = firewalls basés sur l'hôte (host-based).
- D : pfSense/Smoothwall/SonicWall = firewalls réseau (network-based). Note: pfSense est open-source, basé sur FreeBSD.

---

**Q4.** Une ACL standard filtre le trafic en fonction de :
- A) L'adresse IP source et de destination
- B) L'adresse IP source uniquement
- C) L'adresse MAC, l'IP source et le port destination
- D) Le protocole et les flags TCP uniquement

**✅ Réponse : B**
- B : ACL standard = source IP only. C'est la distinction fondamentale avec l'ACL étendue.
- A : Source + destination = ACL étendue.
- C : MAC + port = aussi ACL étendue.
- D : Protocole + TCP flags = critères du filtrage de paquets ou ACL étendue, pas ACL standard.

---

**Q5.** Lequel des éléments suivants n'est PAS une capacité d'un pare-feu ?
- A) Filtrer le trafic entrant et sortant
- B) Effectuer la translation d'adresses réseau (NAT)
- C) Protéger le réseau contre les attaques d'ingénierie sociale
- D) Journaliser les tentatives d'accès

**✅ Réponse : C**
- C : Explicitement listé dans les limitations du cours — le firewall ne peut pas bloquer les attaques basées sur la manipulation des utilisateurs.
- A : Capacité standard.
- B : NAT est une capacité firewall (quand combiné avec un firewall).
- D : Journalisation = capacité standard.

---

## TIER 2: MEDIUM (Scénario)

**Q6.** Un administrateur réseau souhaite bloquer spécifiquement les requêtes HTTP GET et POST entrants provenant d'un réseau externe, tout en autorisant le trafic HTTPS. Quel type de pare-feu est le mieux adapté ?
- A) Filtrage de paquets
- B) Passerelle de niveau circuit
- C) Passerelle de niveau application (Application Gateway)
- D) NAT

**✅ Réponse : C**
- C : Seule la passerelle de niveau application peut inspecter et filtrer des commandes spécifiques à l'application (GET, POST) — c'est son avantage distinctif opérant à la couche 7.
- A : Filtrage de paquets = headers IP/TCP seulement — ne peut pas lire les commandes HTTP.
- B : Circuit-level = handshake TCP uniquement — ne voit pas le contenu HTTP.
- D : NAT traduit des adresses — ne filtre pas le contenu applicatif.

---

**Q7.** Dans une configuration NAT de type NAPT (PAT), dix utilisateurs internes partagent une seule adresse IP publique. Comment le routeur NAT distingue-t-il le trafic de retour destiné à chaque utilisateur ?
- A) Par l'adresse MAC source de chaque utilisateur
- B) Par les numéros de ports source traduits, maintenus dans la table NAT
- C) Par l'adresse IP privée incluse dans le payload du paquet
- D) Par un identifiant de session cryptographique

**✅ Réponse : B**
- B : C'est le mécanisme exact du PAT/NAPT — le routeur maintient une table de translation {IP privée + port interne} ↔ {IP publique + port externe traduit}. La réponse entrante porte le port externe — le routeur la redirige vers l'IP/port interne correspondant.
- A : Les adresses MAC ne traversent pas les routeurs (ne sortent pas du segment L2 local).
- C : Les adresses IP privées ne sont PAS incluses dans le payload — c'est précisément le principe du NAT de les masquer.
- D : Pas de cryptographie dans NAT standard.

---

**Q8.** Un ingénieur déploie un NGFW. Parmi les fonctionnalités suivantes, laquelle N'est PAS disponible dans un pare-feu traditionnel mais EST disponible dans un NGFW ?
- A) Filtrage des paquets IP
- B) Inspection du trafic chiffré SSL/TLS
- C) Translation d'adresses réseau (NAT)
- D) Journalisation du trafic réseau

**✅ Réponse : B**
- B : L'inspection du trafic chiffré (SSL/TLS decryption + DPI) est une fonctionnalité NGFW — les pare-feux traditionnels ne peuvent pas inspecter le contenu chiffré.
- A : Le filtrage de paquets existe dans les pare-feux traditionnels depuis la première génération.
- C : NAT est disponible dans les pare-feux traditionnels.
- D : La journalisation est disponible dans tous les types de pare-feux.

---

**Q9.** Le département IT d'une grande entreprise veut protéger son réseau contre le mouvement latéral après une compromission interne, tout en limitant les coûts matériels. Quelle solution est la plus adaptée ?
- A) Déployer un pare-feu matériel à la frontière du réseau (périmètre uniquement)
- B) Installer un logiciel antivirus sur chaque poste de travail
- C) Déployer des pare-feux internes (internal segmentation firewalls) entre les différents segments du réseau interne
- D) Migrer l'ensemble du réseau vers IPv6

**✅ Réponse : C**
- C : Les pare-feux internes sont explicitement conçus pour bloquer la propagation d'activité malveillante entre segments internes. C'est leur raison d'être selon le cours.
- A : Un FW périmétrique ne voit pas le trafic interne — il ne bloque pas le mouvement latéral.
- B : Un antivirus ne segmente pas le réseau et ne bloque pas le mouvement latéral réseau.
- D : IPv6 ne résout pas le problème de mouvement latéral.

---

**Q10.** Un VPN est configuré pour une connexion site-à-site. L'administrateur veut s'assurer que le trafic VPN peut toujours être inspecté par le pare-feu. Quelle configuration est nécessaire ?
- A) Désactiver le chiffrement VPN pour permettre l'inspection
- B) Le VPN doit effectuer le chiffrement et le déchiffrement EN DEHORS du périmètre de filtrage des paquets
- C) Remplacer le pare-feu par un NGFW uniquement
- D) Utiliser uniquement des tunnels GRE non chiffrés

**✅ Réponse : B**
- B : Textuel du cours — "Un VPN effectue le chiffrement et le déchiffrement en dehors du périmètre de filtrage des paquets, afin de permettre l'inspection des paquets provenant d'autres sites." C'est le design qui permet la coexistence FW + VPN.
- A : Désactiver le chiffrement détruit l'objectif du VPN — inacceptable en termes de sécurité.
- C : Un NGFW peut inspecter le trafic chiffré, mais ce n'est pas la réponse à la question de positionnement.
- D : GRE non chiffré = aucune sécurité pour le transit — non adapté.

---

**Q11.** Quelle est la principale limitation d'une passerelle de niveau circuit (circuit-level gateway) par rapport aux autres technologies de firewall ?
- A) Elle ne peut pas masquer les adresses IP internes
- B) Elle est plus coûteuse que tous les autres types de pare-feux
- C) Elle ne peut pas analyser les contenus actifs et ne gère que les connexions TCP
- D) Elle nécessite un proxy distinct pour chaque application

**✅ Réponse : C**
- C : Limitations explicites du cours — "Ne peut pas analyser les contenus actifs" et "Ne peut gérer que les connexions TCP."
- A : Faux — masquer les IPs internes est précisément un AVANTAGE de la circuit-level gateway.
- B : Elle est décrite comme "relativement peu coûteuse."
- D : C'est l'inverse — un avantage de la circuit-level gateway est qu'elle "ne nécessite pas de serveur proxy distinct pour chaque application."

---

**Q12.** Lors de la configuration des règles d'une ACL, quel principe doit être respecté pour garantir un traitement correct ?
- A) Les règles les plus générales doivent être placées en haut de la liste
- B) Les règles les plus spécifiques doivent être placées en haut de la liste car les ACL sont évaluées de haut en bas
- C) L'ordre des règles n'a pas d'importance, toutes sont évaluées simultanement
- D) La règle "deny all" doit toujours être la première règle de la liste

**✅ Réponse : B**
- B : Principe fondamental des ACLs — évaluation séquentielle top-down. Si une règle générale précède une règle spécifique, la règle générale sera appliquée en premier et la règle spécifique ne sera jamais atteinte.
- A : C'est l'inverse — règles générales en bas, spécifiques en haut.
- C : Complètement faux — le traitement est séquentiel, pas simultané.
- D : "Deny all" est la politique implicite finale (ou explicite en dernière position), pas en première.

---

## TIER 3: HARD (Pièges professeur)

**Q13.** Un administrateur configure un pare-feu avec deux règles :
- Règle 1 : Autoriser tout trafic TCP sur le port 80
- Règle 2 : Bloquer le trafic de l'IP 192.168.1.50 sur le port 80

Quel est le problème avec cette configuration ?
- A) On ne peut pas autoriser le port 80 et bloquer une IP spécifique simultanément
- B) La règle 1 sera évaluée en premier — puisque 192.168.1.50 envoie du trafic TCP sur le port 80, la règle 1 s'applique, et la règle 2 ne sera jamais atteinte
- C) La règle 2 prime toujours sur la règle 1 car elle est plus spécifique
- D) Cette configuration est correcte — le pare-feu appliquera la règle la plus spécifique automatiquement

**✅ Réponse : B**
- B : Les ACLs sont évaluées de HAUT EN BAS. La règle 1 (permit TCP port 80 from any) matche 192.168.1.50 AVANT que la règle 2 soit évaluée. La règle 2 est donc mort-née. Solution correcte : inverser l'ordre (Règle 1: block 192.168.1.50 port 80 ; Règle 2: permit TCP port 80).
- A : Combinaison autorisée — mais l'ordre est le problème, pas la combinaison.
- C : Les firewalls n'appliquent pas automatiquement la règle la plus spécifique — ils suivent l'ordre. C'est une confusion avec les tables de routage (longest prefix match), non avec les ACLs.
- D : Faux — voir explication B.

---

**Q14.** Un attaquant envoie des paquets avec le flag SYN vers un serveur interne via un pare-feu à filtrage de paquets stateless. Le pare-feu autorise le port 80. L'attaquant envoie ensuite des paquets sans flag SYN (mid-session) avec une IP forgée prétendant appartenir à une session établie. Quel est le résultat ?
- A) Le pare-feu bloque tous les paquets sans SYN flag car ils ne sont pas des nouvelles connexions
- B) Le pare-feu stateless autorise les paquets car ils correspondent à la règle port 80, sans vérifier s'ils appartiennent à une session établie
- C) Le pare-feu stateless vérifie la state table et rejette les paquets non associés à une session
- D) Le pare-feu déclenche une alarme IPS automatiquement

**✅ Réponse : B**
- B : C'est exactement la vulnérabilité du filtrage stateless — chaque paquet est évalué INDÉPENDAMMENT. Un paquet sur port 80 correspond à la règle "allow port 80" qu'il appartienne à une session légitime ou non. Il n'y a pas de state table dans un filtre stateless.
- A : Un filtre stateless ne distingue pas les paquets SYN des paquets mid-session — il voit juste "port 80, TCP" → match la règle.
- C : State table = stateful inspection uniquement. Stateless n'a pas de state table.
- D : IPS intégré = NGFW, pas un simple filtre de paquets.

---

**Q15.** Un administrateur déploie un pare-feu de type application proxy pour protéger le réseau. Un utilisateur se plaint que sa connexion est plus lente depuis le déploiement. L'administrateur explique que c'est normal. Parmi les raisons suivantes, laquelle est CORRECTE ?
- A) Le proxy applicatif chiffre tout le trafic, ce qui ralentit les connexions
- B) Le proxy applicatif génère de nouveaux paquets IP pour le client et inspecte le contenu à la couche applicative, ce qui introduit une latence inhérente
- C) Le proxy applicatif est mal configuré et doit être redémarré
- D) Les pare-feux applicatifs sont toujours plus lents que les connexions non filtrées car ils doublent la bande passante utilisée

**✅ Réponse : B**
- B : La latence du proxy applicatif est inhérente à son fonctionnement — il termine la connexion côté client, inspecte le contenu à L7, génère de nouveaux paquets IP, et établit une nouvelle connexion côté serveur. Ce double-processing introduit de la latence. Ce n'est pas une erreur de configuration.
- A : Le proxy applicatif n'effectue pas de chiffrement par défaut — c'est la fonction du VPN ou SSL/TLS.
- C : La lenteur est normale et attendue, pas signe d'une mauvaise configuration.
- D : Le proxy ne double pas la bande passante — il intercepte et re-émet. La bande passante n'est pas doublée.

---

**Q16.** NAT est décrit dans le cours comme une "technologie de routage" qui devient une "technologie de firewall" lorsque combinée avec un pare-feu. Quelle propriété de sécurité justifie cette classification ?
- A) NAT chiffre automatiquement tout le trafic qui traverse le routeur
- B) NAT peut autoriser uniquement les connexions initiées depuis le réseau interne, bloquant ainsi les connexions initiées de l'extérieur, ce qui crée un comportement de filtrage directionnel
- C) NAT masque les adresses IP internes, ce qui rend les attaques DDoS impossibles
- D) NAT fragmente les paquets pour empêcher leur inspection par des attaquants

**✅ Réponse : B**
- B : Textuel du cours — "Le NAT peut aussi agir comme une technique de filtrage de pare-feu, en autorisant uniquement les connexions initiées depuis le réseau interne, et en bloquant celles provenant de l'extérieur." C'est une propriété de sécurité intrinsèque du NAT dynamique.
- A : NAT n'effectue aucun chiffrement.
- C : Masquer les IPs internes réduit la surface de reconnaissance (reconnaissance), mais ne rend pas les DDoS impossibles — le DDoS cible l'IP publique, pas les IPs internes.
- D : NAT ne fragmente pas les paquets de manière intentionnelle.

---

**Q17.** Une passerelle de niveau application proxy reçoit une requête HTTPS chiffrée d'un client externe. Sans configuration spécifique, quelle est la limitation de ce proxy ?
- A) Il rejette automatiquement tout le trafic HTTPS car ce protocole n'est pas supporté
- B) Il ne peut pas inspecter le contenu chiffré du trafic HTTPS sans être configuré pour l'inspection SSL/TLS, donc il ne peut pas filtrer les commandes applicatives dans ce trafic
- C) Il déchiffre automatiquement le trafic HTTPS grâce à sa position sur la couche applicative
- D) Il redirige le trafic HTTPS vers le pare-feu matériel pour inspection

**✅ Réponse : B**
- B : Explicitement dans le cours — "Si le proxy n'est pas configuré pour l'inspection SSL/TLS, il ne peut pas filtrer ni examiner les paquets chiffrés." C'est une limitation critique des proxies applicatifs face au chiffrement end-to-end.
- A : Les proxies applicatifs supportent HTTPS — mais ils voient seulement un tunnel chiffré sans SSL inspection.
- C : Un proxy ne déchiffre pas automatiquement sans la clé appropriée et une configuration SSL inspection.
- D : La redirection vers le firewall matériel n'est pas le comportement décrit.

---

**Q18.** Un pare-feu correctement configuré est en place dans une organisation. Un employé reçoit un courriel contenant un lien malveillant, clique dessus, et son poste est infecté par un malware qui établit une connexion sortante vers un serveur C2 sur le port 443 (HTTPS). Le pare-feu a-t-il échoué ?
- A) Oui — un pare-feu correctement configuré aurait dû bloquer toute connexion sortante sur le port 443
- B) Non — cette attaque exploite deux limitations connues du pare-feu : l'ingénierie sociale (clic utilisateur) et le trafic via ports courants (443), que le cours liste explicitement comme des limites
- C) Oui — le pare-feu aurait dû détecter le malware et bloquer la connexion C2
- D) Non — le pare-feu a fonctionné correctement car il a laissé passer le trafic HTTPS légitime

**✅ Réponse : B**
- B : Le cours liste explicitement deux limitations correspondantes : (1) "Un pare-feu ne protège pas contre les attaques d'ingénierie sociale" (le clic sur le lien) et (2) "Un pare-feu ne protège pas contre les attaques utilisant des ports ou des applications courants" (port 443). Le pare-feu n'a pas "échoué" — il a agi selon ses capacités. C'est une limite connue et documentée.
- A : Bloquer le port 443 briserait tout le trafic HTTPS légitime — contre-productif.
- C : Détecter les malwares = fonction antivirus/EDR, pas du pare-feu standard. Même un NGFW nécessite des signatures pour détecter ce comportement.
- D : Partiellement correct mais incomplet — la question demande si le FW a "échoué", et la réponse complète est B.
