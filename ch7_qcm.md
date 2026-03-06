# CH7 — VPN : BANQUE QCM (3 NIVEAUX)

---

## NIVEAU FACILE — 5 Questions (Rappel direct)

---

**Q1. Quelle est la fonctionnalité principale qui permet à un VPN de masquer les adresses IP réelles des machines internes ?**

A) Le chiffrement  
B) L'authentification  
C) L'encapsulation  
D) Le tunneling SSL  

✅ **Réponse : C**

**Pourquoi C :** L'encapsulation enferme le paquet original dans un nouveau paquet portant les adresses des équipements VPN. Ce sont ces nouvelles adresses qui circulent sur Internet, masquant les adresses réelles du réseau interne.

**Pourquoi pas A :** Le chiffrement rend le contenu illisible mais ne masque pas structurellement les adresses IP (sans encapsulation, les adresses source/destination restent visibles dans l'en-tête externe).

**Pourquoi pas B :** L'authentification vérifie l'identité des utilisateurs, elle n'agit pas sur la visibilité des adresses réseau.

**Pourquoi pas D :** SSL/TLS est une technologie de chiffrement, non un mécanisme de masquage d'adresses.

---

**Q2. Quel composant VPN joue le rôle de point terminal de tunnel bidirectionnel et gère les 8 fonctions clés du VPN (chiffrement, authentification, gestion des clés...) ?**

A) Client VPN  
B) NAS (Network Access Server)  
C) Concentrateur VPN  
D) Pare-feu avec option VPN  

✅ **Réponse : C**

**Pourquoi C :** Le concentrateur VPN est défini comme le point terminal de tunnel bidirectionnel. Il assure : chiffrement/déchiffrement, authentification, gestion de transfert, négociation des paramètres, gestion des clés, établissement des tunnels, assignation d'adresses, gestion des flux E/S.

**Pourquoi pas A :** Le client VPN initie la connexion depuis le poste utilisateur, il n'est pas le point terminal central.

**Pourquoi pas B :** Le NAS est un serveur d'accès réseau intermédiaire (point d'entrée réseau), pas le gestionnaire de tunnels VPN.

**Pourquoi pas D :** Le pare-feu filtre le trafic, il peut intégrer une fonction VPN basique mais n'est pas le concentrateur dédié.

---

**Q3. Un VPN de type "Trusted VPN" utilise quelle technologie comme infrastructure de transport ?**

A) IPsec et SSL  
B) OpenVPN et 3DES  
C) ATM, Frame Relay et MPLS  
D) PPTP et L2TP  

✅ **Réponse : C**

**Pourquoi C :** Les Trusted VPNs reposent sur des circuits loués auprès d'opérateurs de communication — ATM (Asynchronous Transfer Mode), Frame Relay et MPLS (Multiprotocol Label Switching).

**Pourquoi pas A :** IPsec et SSL sont des protocoles de Secure VPN.

**Pourquoi pas B :** OpenVPN et 3DES sont des technologies de chiffrement pour Secure VPN.

**Pourquoi pas D :** PPTP et L2TP sont des protocoles de tunneling, pas des technologies de transport opérateur.

---

**Q4. Quel type de VPN ne nécessite pas de logiciel client pré-installé sur le poste utilisateur ?**

A) VPN IPsec d'accès distant  
B) VPN SSL (basé sur le Web)  
C) VPN Hardware  
D) VPN L2TP/IPsec  

✅ **Réponse : B**

**Pourquoi B :** Le VPN SSL utilise le navigateur web et les composants nécessaires sont téléchargés dynamiquement lors de la connexion. Aucune installation préalable de client dédié n'est requise.

**Pourquoi pas A :** IPsec d'accès distant nécessite un logiciel client VPN dédié sur le poste.

**Pourquoi pas C :** Le VPN Hardware est un dispositif physique côté réseau, pas lié à l'absence de client.

**Pourquoi pas D :** L2TP/IPsec nécessite un client VPN configuré sur le poste.

---

**Q5. Dans une topologie VPN Hub-and-Spoke, que se passe-t-il si le hub tombe en panne ?**

A) Les spokes communiquent directement entre eux en mode fallback  
B) Seules les connexions actives sont interrompues  
C) Toutes les connexions entre spokes sont interrompues  
D) Le hub secondaire prend automatiquement le relais  

✅ **Réponse : C**

**Pourquoi C :** Dans Hub-and-Spoke, toute communication entre branches passe obligatoirement par le hub. Sa panne coupe donc l'intégralité du réseau VPN. C'est l'inconvénient critique de cette topologie.

**Pourquoi pas A :** Les spokes ne peuvent pas communiquer directement — c'est précisément le principe de Hub-and-Spoke.

**Pourquoi pas B :** Ce n'est pas "seules les actives" — toutes sont perdues, y compris les potentielles futures.

**Pourquoi pas D :** Des hubs secondaires optionnels peuvent exister pour la résilience, mais cela doit être préconfiguré — ce n'est pas automatique par défaut.

---

## NIVEAU MOYEN — 7 Questions (Compréhension mécanisme)

---

**Q6. Une entreprise veut connecter son siège à 3 agences distantes. Les agences doivent accéder aux ressources du siège mais ne doivent jamais communiquer directement entre elles. Quelle topologie est la plus appropriée ?**

A) Full Mesh VPN  
B) Point-to-Point VPN  
C) Star Topology  
D) Hub-and-Spoke  

✅ **Réponse : C**

**Pourquoi C :** La topologie Star interdit explicitement l'interconnexion directe entre succursales. Toute communication passe par le siège (site central). Elle est typiquement déployée dans des environnements financiers (banques) pour cette raison exacte.

**Pourquoi pas A :** Full Mesh permet la communication directe entre tous les pairs — contraire à l'exigence.

**Pourquoi pas B :** Point-to-Point ne gère que 2 sites.

**Pourquoi pas D :** Hub-and-Spoke permet techniquement aux spokes de communiquer entre eux via le hub. La Star est plus stricte dans l'interdiction directe d'inter-succursales.

---

**Q7. Un paquet est envoyé depuis Network 1 (IP: 10.0.50.3) via un tunnel VPN. Le paquet encapsulé circule sur Internet avec l'IP source 192.168.50.1. Que représente cette IP 192.168.50.1 ?**

A) L'adresse IP du client final  
B) L'adresse IP du routeur VPN (passerelle) du réseau émetteur  
C) L'adresse IP du serveur d'authentification  
D) L'adresse IP virtuelle attribuée par le concentrateur VPN  

✅ **Réponse : B**

**Pourquoi B :** L'encapsulation VPN remplace l'adresse IP source originale (10.0.50.3) par l'adresse IP du routeur VPN côté émetteur (192.168.50.1). C'est cette adresse de passerelle VPN qui est visible sur Internet.

**Pourquoi pas A :** L'IP du client (10.0.50.3) est encapsulée et invisible de l'extérieur.

**Pourquoi pas C :** Le serveur d'authentification est une entité distincte dans l'architecture VPN.

**Pourquoi pas D :** Les adresses virtuelles sont assignées aux clients VPN pour leur permettre d'apparaître comme étant dans le réseau interne, pas pour l'en-tête externe.

---

**Q8. Quelle est la différence fondamentale entre un VPN d'accès distant basé sur l'intranet et un VPN site-à-site basé sur l'extranet ?**

A) L'intranet VPN utilise IPsec, l'extranet utilise SSL  
B) L'intranet connecte des sites d'une même organisation, l'extranet connecte des organisations différentes  
C) L'intranet est permanent, l'extranet est à la demande  
D) L'intranet chiffre les données, l'extranet ne les chiffre pas  

✅ **Réponse : B**

**Pourquoi B :** La distinction est purement organisationnelle : le VPN intranet relie des sites d'une **même organisation** (agences au siège), le VPN extranet connecte l'entreprise à des **organisations tierces** (fournisseurs, clients, partenaires). La config extranet empêche aussi l'accès au VPN intranet.

**Pourquoi pas A :** Les deux peuvent utiliser IPsec ou SSL — le protocole n'est pas le critère de distinction.

**Pourquoi pas C :** Les deux types de VPN site-à-site sont généralement des connexions permanentes.

**Pourquoi pas D :** Les deux chiffrent les données — c'est une caractéristique commune de tout Secure VPN.

---

**Q9. Un administrateur doit déployer rapidement un VPN pour une PME avec budget limité. Il n'a pas de matériel dédié disponible. Quelle solution est recommandée et quels sont ses inconvénients principaux ?**

A) VPN Hardware — inconvénients : coûteux et scalabilité limitée  
B) VPN Software — inconvénients : charge CPU sur l'hôte et moins sécurisé  
C) VPN Trusted — inconvénients : pas de chiffrement  
D) Full Mesh VPN — inconvénients : nombre de tunnels exponentiellement croissant  

✅ **Réponse : B**

**Pourquoi B :** Le VPN Software est la solution pour PME sans matériel dédié : aucun équipement supplémentaire, déploiement simple, peu coûteux, ne modifie pas le réseau cible. Inconvénients : charge de traitement additionnelle sur l'équipement hôte, moins sécurisé et plus vulnérable aux attaques.

**Pourquoi pas A :** VPN Hardware est précisément ce qu'il n'a pas — et c'est plus cher, inadapté aux petites structures.

**Pourquoi pas C :** Trusted VPN n'est pas une option pratique pour PME — nécessite des circuits loués chez opérateur télécom.

**Pourquoi pas D :** Full Mesh est une topologie, pas un type d'implémentation VPN.

---

**Q10. Dans le processus d'authentification VPN, à quelle étape les points de terminaison doivent-ils s'authentifier ?**

A) Après l'établissement du tunnel chiffré  
B) Avant l'établissement de la connexion VPN  
C) Pendant la transmission des données  
D) Seulement lors de la première connexion, puis mémorisé  

✅ **Réponse : B**

**Pourquoi B :** L'authentification intervient AVANT l'établissement de la connexion VPN. Le flux est : connexion Internet → initialisation VPN → authentification des endpoints → établissement du tunnel → accès sécurisé au réseau.

**Pourquoi pas A :** Authentifier après le tunnel serait une faille majeure — n'importe qui pourrait initier un tunnel puis être authentifié.

**Pourquoi pas C :** L'authentification est un processus d'établissement de session, pas continu pendant le transfert de données (à ne pas confondre avec l'intégrité des paquets en continu).

**Pourquoi pas D :** Bien que des mécanismes de SSO existent, l'authentification VPN n'est pas "mémorisée" de façon permanente — chaque nouvelle session nécessite une authentification.

---

**Q11. Quelle est la particularité de l'algorithme L2TP qui le rend souvent associé à IPsec dans les déploiements VPN ?**

A) L2TP ne supporte pas le tunneling de couche 2  
B) L2TP ne fournit pas de chiffrement natif des données  
C) L2TP est incompatible avec les navigateurs web modernes  
D) L2TP chiffre les données mais n'encapsule pas les paquets  

✅ **Réponse : B**

**Pourquoi B :** L2TP (Layer 2 Tunneling Protocol) assure le **tunneling** (encapsulation de couche 2) mais n'intègre **pas de mécanisme de chiffrement**. C'est pourquoi il est systématiquement couplé à IPsec pour la sécurité : L2TP assure le tunnel, IPsec assure le chiffrement et l'authentification.

**Pourquoi pas A :** L2TP est précisément un protocole de tunneling de couche 2.

**Pourquoi pas C :** La compatibilité navigateur concerne SSL VPN, pas L2TP.

**Pourquoi pas D :** C'est l'inverse — L2TP encapsule mais ne chiffre pas.

---

**Q12. Quels avantages le VPN Hardware offre-t-il par rapport au VPN Software, particulièrement dans un contexte de nombreux clients simultanés ?**

A) Il est plus économique et plus simple à configurer  
B) Il offre du load balancing natif et une sécurité renforcée  
C) Il ne nécessite pas de matériel supplémentaire  
D) Il est accessible via navigateur web sans client dédié  

✅ **Réponse : B**

**Pourquoi B :** Le VPN Hardware offre du load balancing intégré (particulièrement utile avec de nombreux clients) et une sécurité plus élevée car le traitement cryptographique est déporté sur un matériel dédié, non partagé avec d'autres applications.

**Pourquoi pas A :** C'est l'inverse — le VPN Hardware est plus coûteux et plus complexe à configurer.

**Pourquoi pas C :** Le VPN Hardware nécessite précisément du matériel supplémentaire (équipement dédié).

**Pourquoi pas D :** L'accès via navigateur sans client est une caractéristique du VPN SSL, pas du Hardware VPN.

---

## NIVEAU DIFFICILE — 6 Questions (Pièges professeurs)

---

**Q13. Un Secure VPN garantit :**

A) L'intégrité du chemin de transmission et la confidentialité des données  
B) La confidentialité et l'intégrité des données, mais pas le chemin de transmission  
C) Le contrôle du chemin par l'organisation et le chiffrement des données  
D) La confidentialité des données et la disponibilité garantie du chemin  

✅ **Réponse : B**

**Pourquoi B :** Le Secure VPN chiffre les données de bout en bout (confidentialité + intégrité des données). Mais le chemin de transmission emprunte Internet public — l'organisation ne contrôle pas quels nœuds les paquets traversent. C'est le Trusted VPN qui garantit le chemin, pas le Secure VPN.

**Pourquoi pas A :** "Intégrité du chemin" est la caractéristique du Trusted VPN, pas du Secure VPN.

**Pourquoi pas C :** "Contrôle du chemin" = Trusted VPN uniquement.

**Pourquoi pas D :** La "disponibilité garantie du chemin" est une propriété opérateur (Trusted VPN), pas des Secure VPNs.

---

**Q14. Dans une topologie Hub-and-Spoke VPN, une succursale A veut envoyer des données à une succursale B. Quel est le chemin réel des données ?**

A) A → Internet → B (direct)  
B) A → Hub → A (retour d'erreur)  
C) A → Hub → B  
D) A → Internet → Hub → Internet → B  

✅ **Réponse : C (et D est techniquement plus précis)**

**La réponse attendue académiquement : D**

**Pourquoi D :** Dans une architecture Hub-and-Spoke réelle, les données de A traversent Internet pour atteindre le Hub, puis traversent à nouveau Internet (ou le même réseau) pour atteindre B. Le Hub est le point central de relais. Le trafic passe physiquement deux fois par Internet dans une implémentation typique.

**Pourquoi pas A :** Les spokes ne peuvent pas communiquer directement — c'est le principe fondamental de Hub-and-Spoke.

**Pourquoi pas B :** Il n'y a pas de retour d'erreur — les données sont transférées via le hub vers B.

**Piège :** C est logiquement correct (A→Hub→B) mais simplifie le fait que physiquement Internet est traversé deux fois (A→Internet→Hub→Internet→B).

---

**Q15. Une organisation déploie un VPN IPsec site-à-site extranet avec un partenaire. Quelle propriété est automatiquement garantie par cette configuration ?**

A) Le partenaire peut accéder au VPN intranet de l'organisation  
B) L'organisation peut accéder au réseau interne du partenaire sans restriction  
C) La configuration extranet empêche l'accès au VPN intranet  
D) L'extranet VPN utilise SSL au lieu d'IPsec  

✅ **Réponse : C**

**Pourquoi C :** Par définition, la configuration d'un VPN extranet empêche le partenaire d'accéder au VPN intranet de l'organisation. C'est une propriété de segmentation de sécurité fondamentale des VPN extranet — les deux réseaux sont connectés pour des échanges spécifiques mais l'intranet reste protégé.

**Pourquoi pas A :** C'est l'inverse — la config extranet empêche précisément cet accès.

**Pourquoi pas B :** L'accès est limité aux ressources partagées définies, pas illimité.

**Pourquoi pas D :** L'extranet VPN peut utiliser IPsec — le protocole n'est pas lié au type intranet/extranet.

---

**Q16. Quelle affirmation sur le VPN SSL est FAUSSE ?**

A) Il ne nécessite pas l'installation préalable d'un logiciel client  
B) Les composants nécessaires sont téléchargés dynamiquement  
C) Il offre une connexion identique depuis des postes gérés et non gérés  
D) Il nécessite un certificat client installé sur chaque poste avant connexion  

✅ **Réponse : D**

**Pourquoi D est FAUX :** Le VPN SSL est précisément conçu pour ne pas nécessiter d'installation préalable. Aucun certificat client ne doit être pré-installé. C'est sa caractéristique différenciatrice : les composants sont téléchargés dynamiquement lors de la connexion.

**Pourquoi pas A (elle est vraie) :** Par définition du VPN SSL — pas d'installation préalable.

**Pourquoi pas B (elle est vraie) :** Caractéristique explicite du VPN SSL dans le cours.

**Pourquoi pas C (elle est vraie) :** Le VPN SSL peut être utilisé depuis des postes gérés (entreprise) ET non gérés (PC personnel, sous-traitants, partenaires).

---

**Q17. Dans quel scénario le VPN Full Mesh est-il MOINS adapté que le Hub-and-Spoke ?**

A) Quand la redondance et la haute disponibilité sont des priorités  
B) Quand l'organisation veut éviter un point de défaillance unique  
C) Quand le réseau contient un grand nombre de sites et que la gestion doit rester simple  
D) Quand des communications directes entre sites sont nécessaires sans latence additionnelle  

✅ **Réponse : C**

**Pourquoi C :** Full Mesh crée un tunnel IPsec entre chaque paire de sites. Pour N sites, cela fait N(N-1)/2 tunnels. Avec un grand nombre de sites, la gestion devient exponentiellement complexe (configurations, certificats, maintenance). Hub-and-Spoke est bien plus simple à gérer à grande échelle.

**Pourquoi pas A :** Full Mesh EST mieux pour la redondance et haute disponibilité — c'est un de ses avantages.

**Pourquoi pas B :** Full Mesh élimine le point de défaillance unique (contrairement à Hub-and-Spoke). Donc pour éviter ce SPOF, Full Mesh est meilleur.

**Pourquoi pas D :** Full Mesh est meilleur pour la latence (communication directe sans passer par hub).

---

**Q18. Un administrateur réseau configure un VPN et choisit un VPN de type "Trusted". Quel risque de sécurité est-il en train d'accepter par rapport à un Secure VPN ?**

A) Le chemin de transmission n'est pas contrôlé et peut être intercepté  
B) Les données transmises ne sont pas chiffrées et peuvent être lues par l'opérateur télécom  
C) L'authentification des utilisateurs n'est pas possible dans ce type de VPN  
D) Les tunnels VPN peuvent être établis sans négociation de paramètres de sécurité  

✅ **Réponse : B**

**Pourquoi B :** Le Trusted VPN ne fournit pas de chiffrement des données — l'opérateur garantit l'intégrité du chemin mais les données circulent sans chiffrement. L'opérateur télécom (ou un attaquant ayant accès à l'infrastructure opérateur) peut potentiellement lire les données.

**Pourquoi pas A :** C'est le risque du Secure VPN (pas de contrôle chemin), pas du Trusted VPN. Le Trusted VPN contrôle précisément le chemin.

**Pourquoi pas C :** L'authentification peut être implémentée indépendamment du chiffrement.

**Pourquoi pas D :** La négociation des paramètres de sécurité est une fonction du concentrateur VPN, indépendante du type Trusted/Secure.
