# CH6 — SERVEUR PROXY : BANQUE QCM

---

## TIER 1 — FACILE (Rappel direct, définitions)

---

**Q1. Qu'est-ce qu'un serveur proxy ?**

A) Un routeur qui connecte deux réseaux différents  
B) Un système intermédiaire situé entre un client et un serveur réel qui traite les requêtes au nom du client  
C) Un pare-feu qui filtre les paquets au niveau réseau  
D) Un système de chiffrement des communications entre client et serveur  

✅ **Réponse : B**

**Pourquoi B est correct :** La définition académique précise que le proxy est un intermédiaire (mandataire) qui reçoit les requêtes du client, les traite, et communique avec le serveur réel au nom du client — c'est l'essence de l'intermédiation proxy.

**Pourquoi A est faux :** Un routeur relaie les paquets entre réseaux au niveau 3 sans terminer les connexions ni inspecter le contenu.

**Pourquoi C est faux :** Le filtrage de paquets est une fonction de firewall (niveau 3-4), pas de proxy (niveau 7). Le proxy inspecte la charge utile, pas seulement les en-têtes.

**Pourquoi D est faux :** Le chiffrement est la fonction d'un VPN ou TLS, pas d'un proxy en tant que tel (bien que certains proxies puissent inspecter du TLS via SSL bumping).

---

**Q2. Sur quelle couche du modèle OSI opère principalement un serveur proxy ?**

A) Couche 3 (Réseau)  
B) Couche 4 (Transport)  
C) Couche 5 (Session)  
D) Couche 7 (Application)  

✅ **Réponse : D**

**Pourquoi D est correct :** Le proxy inspecte la charge utile applicative — il comprend HTTP, FTP, SMTP, etc. — ce qui le situe par définition à la couche application (couche 7).

**Pourquoi A est faux :** La couche réseau est celle des firewalls à filtrage de paquets et des routeurs.

**Pourquoi B est faux :** La couche transport gère TCP/UDP — les firewalls stateful opèrent à cette couche.

**Pourquoi C est faux :** SOCKS opère à la couche session (couche 5), mais un proxy HTTP standard est couche 7.

---

**Q3. Quel est le port par défaut du protocole SOCKS ?**

A) 80  
B) 3128  
C) 1080  
D) 8080  

✅ **Réponse : C**

**Pourquoi C est correct :** Le port 1080 est le port standard défini pour SOCKS par l'IETF.

**Pourquoi A est faux :** 80 est le port HTTP.

**Pourquoi B est faux :** 3128 est le port par défaut de Squid Proxy.

**Pourquoi D est faux :** 8080 est un port alternatif HTTP/proxy générique, non le standard SOCKS.

---

**Q4. Quel type de proxy est invisible pour l'utilisateur final et ne nécessite aucune configuration du client ?**

A) Proxy non-transparent  
B) Proxy SOCKS  
C) Proxy transparent  
D) Proxy inverse  

✅ **Réponse : C**

**Pourquoi C est correct :** Le proxy transparent est configuré pour être totalement invisible pour l'utilisateur final — il intercepte le trafic de manière automatique sans que le client en ait connaissance.

**Pourquoi A est faux :** Le proxy non-transparent nécessite que le client soit configuré et que le client soit informé de l'existence du proxy.

**Pourquoi B est faux :** SOCKS nécessite que le client soit configuré pour utiliser le serveur SOCKS.

**Pourquoi D est faux :** Le proxy inverse protège les serveurs, pas les clients, et opère du côté serveur — ce n'est pas le concept décrit.

---

**Q5. Quel proxy Squid constitue un exemple concret dans le cours ?**

A) Un proxy SOCKS de tunneling  
B) Un proxy de mise en cache supportant HTTP, HTTPS et FTP  
C) Un proxy inverse pour load balancing  
D) Un proxy anonyme pour contourner la censure  

✅ **Réponse : B**

**Pourquoi B est correct :** Squid est explicitement décrit comme un "proxy de mise en cache pour le Web" supportant HTTP, HTTPS, FTP et d'autres protocoles, réduisant la bande passante via le cache.

**Pourquoi A est faux :** Squid n'est pas un proxy SOCKS — c'est un proxy HTTP/cache.

**Pourquoi C est faux :** Nginx/HAProxy sont des reverse proxies de load balancing, pas Squid.

**Pourquoi D est faux :** Squid peut être configuré avec du filtrage (SquidGuard) mais sa fonction primaire est le cache HTTP, pas l'anonymat.

---

## TIER 2 — MOYEN (Scénarios, mécanismes)

---

**Q6. Un administrateur réseau constate qu'après une panne matérielle du serveur proxy, tous les utilisateurs internes ont perdu l'accès à Internet. Quelle caractéristique du proxy explique ce comportement ?**

A) Le proxy utilise un protocole de routage défaillant  
B) Le proxy adopte un comportement fail-open en cas de panne  
C) Le proxy est un point de défaillance unique avec comportement fail-closed  
D) Le firewall a détecté la panne et a bloqué le trafic  

✅ **Réponse : C**

**Pourquoi C est correct :** En cas de panne du serveur proxy, toutes les communications réseau s'arrêtent — c'est le comportement fail-closed inhérent à l'architecture proxy. Le proxy étant le seul chemin vers Internet, sa panne crée un SPOF.

**Pourquoi A est faux :** Le proxy n'utilise pas de protocole de routage dynamique — il gère des connexions applicatives, pas du routage.

**Pourquoi B est faux :** C'est l'INVERSE — le proxy est fail-closed (tout s'arrête), c'est le filtre de paquets qui est fail-open.

**Pourquoi D est faux :** Le firewall n'a pas nécessairement connaissance de la panne du proxy — c'est la panne du proxy lui-même qui coupe le flux.

---

**Q7. Lors du traitement d'une requête, un serveur proxy reconstruit le paquet avec une nouvelle adresse IP source. Quel objectif de sécurité cela accomplit-il ?**

A) Il chiffre le contenu du paquet pour protéger les données en transit  
B) Il masque l'identité de l'hôte interne réel auprès du serveur cible  
C) Il augmente la vitesse de transmission en réduisant la taille du paquet  
D) Il vérifie l'intégrité du paquet via un checksum  

✅ **Réponse : B**

**Pourquoi B est correct :** En substituant sa propre adresse IP à celle du client interne, le proxy masque la topologie du réseau interne — le serveur cible ne peut pas déterminer l'IP réelle du client.

**Pourquoi A est faux :** La reconstruction du paquet avec nouvelle IP n'implique aucun chiffrement du contenu.

**Pourquoi C est faux :** La reconstruction ne réduit pas la taille — elle change l'en-tête IP source. L'accélération vient du cache, pas de la reconstruction.

**Pourquoi D est faux :** La vérification d'intégrité par checksum est une fonction TCP/IP standard, pas spécifique à la reconstruction par proxy.

---

**Q8. Une entreprise souhaite déployer un proxy qui offre des services de transformation de types de médias, d'annotation de groupe et de réduction de protocole. Quel type de proxy doit-elle choisir ?**

A) Proxy transparent  
B) Proxy non-transparent  
C) Proxy SOCKS  
D) Proxy anonyme  

✅ **Réponse : B**

**Pourquoi B est correct :** Ces quatre services (annotation de groupe, transformation de médias, réduction de protocole, filtrage pour l'anonymat) sont explicitement attribués au proxy non-transparent dans le cours.

**Pourquoi A est faux :** Le proxy transparent ne propose pas ces services additionnels — il se limite à l'interception et au forwarding.

**Pourquoi C est faux :** SOCKS est agnostique au protocole et ne comprend pas les types de médias ou les protocoles applicatifs.

**Pourquoi D est faux :** Le proxy anonyme est centré sur le masquage de l'IP, pas sur la transformation de contenu.

---

**Q9. Quelle est la différence fondamentale entre le proxy SOCKS et un proxy HTTP en termes de capacités ?**

A) SOCKS est plus rapide car il opère à une couche inférieure et possède un cache intégré  
B) SOCKS est agnostique au protocole applicatif et ne dispose pas de cache HTTP, contrairement au proxy HTTP  
C) SOCKS ne supporte que TCP, contrairement au proxy HTTP qui supporte TCP et UDP  
D) SOCKS est standardisé par l'IEEE, le proxy HTTP par l'IETF  

✅ **Réponse : B**

**Pourquoi B est correct :** SOCKS crée un tunnel générique au niveau socket (agnostique au protocole) et n'a pas de capacités de mise en cache HTTP — c'est sa distinction fondamentale avec le proxy HTTP.

**Pourquoi A est faux :** SOCKS n'a PAS de cache — c'est l'inverse. Le proxy HTTP avec cache est plus rapide pour les ressources déjà cachées.

**Pourquoi C est faux :** SOCKS5 supporte TCP ET UDP — c'est précisément l'un de ses avantages par rapport à SOCKS4.

**Pourquoi D est faux :** Les deux sont des standards IETF. SOCKS5 est défini dans la RFC 1928.

---

**Q10. Un administrateur examine les logs réseau après un incident. Il constate que les logs du proxy contiennent les URLs complètes visitées, les User-Agents et les contenus des réponses HTTP. Les logs du firewall ne contiennent que les adresses IP et les ports. Cette différence est due à :**

A) Une mauvaise configuration du firewall qui n'enregistre pas assez d'informations  
B) Le proxy inspecte la charge utile (payload) complète tandis que le firewall n'examine que les en-têtes de paquets  
C) Le firewall ne supporte pas les connexions HTTP  
D) Les logs du proxy sont plus récents et donc plus détaillés  

✅ **Réponse : B**

**Pourquoi B est correct :** C'est précisément la distinction architecturale — le proxy opère en couche 7 et inspecte le payload complet, permettant des logs applicatifs riches. Le firewall (filtre de paquets) opère en couche 3-4 et ne voit que les en-têtes.

**Pourquoi A est faux :** Ce n'est pas une mauvaise configuration — c'est une limitation inhérente au fonctionnement du filtre de paquets.

**Pourquoi C est faux :** Les firewalls supportent parfaitement les connexions HTTP — ils les filtrent simplement au niveau des en-têtes (IP, port), pas du contenu.

**Pourquoi D est faux :** La richesse des logs n'est pas une question de récence mais d'architecture — couche OSI et profondeur d'inspection.

---

**Q11. Quel est l'avantage sécuritaire du proxy inverse (reverse proxy) par rapport à l'exposition directe d'un serveur web sur Internet ?**

A) Il chiffre toutes les communications entre le client et le serveur web  
B) Il permet au client de savoir exactement quel serveur backend traite sa requête  
C) Il masque l'infrastructure backend — le serveur réel n'est jamais exposé directement à Internet  
D) Il élimine la nécessité d'un certificat SSL sur le serveur backend  

✅ **Réponse : C**

**Pourquoi C est correct :** Le proxy inverse agit comme façade — le client ne communique qu'avec le proxy inverse, jamais avec le serveur backend réel. Cela masque la topologie, protège contre les attaques directes, et permet une protection DDoS en amont.

**Pourquoi A est faux :** Le proxy inverse peut faire de la SSL termination (décryptage au niveau du proxy), ce qui signifie que la communication interne vers le backend peut être non chiffrée.

**Pourquoi B est faux :** C'est l'INVERSE — le client ignore quel serveur backend traite sa requête. C'est précisément l'objectif.

**Pourquoi D est faux :** La SSL termination est optionnelle — le proxy inverse peut également fonctionner en SSL passthrough où le certificat reste sur le backend.

---

**Q12. Dans quel scénario le comportement "fail-open" d'un filtre de paquets est-il plus dangereux que le comportement "fail-closed" d'un proxy ?**

A) Dans un réseau où la disponibilité est critique et où les pannes doivent maintenir l'accès  
B) Dans un réseau sécurisé où la panne d'un composant ne doit pas ouvrir de brèche dans la sécurité  
C) Dans un réseau public où tous les utilisateurs sont de confiance  
D) Dans un réseau où le trafic chiffré est majoritaire  

✅ **Réponse : B**

**Pourquoi B est correct :** Dans un réseau sécurisé, si le filtre de paquets tombe en panne et laisse passer tout le trafic (fail-open), cela crée une brèche de sécurité critique — des attaques qui auraient été bloquées passent librement. Le fail-closed du proxy est plus sécurisant dans ce contexte, même si impactant pour la disponibilité.

**Pourquoi A est faux :** C'est le cas où le fail-open est préféré (disponibilité > sécurité) — mais cela ne le rend pas "moins dangereux", ça justifie juste le choix opérationnel.

**Pourquoi C est faux :** Si tous les utilisateurs sont de confiance, le fail-open est moins risqué. Mais dans ce cas, la sécurité n'est pas le critère principal.

**Pourquoi D est faux :** La nature chiffrée ou non du trafic n'est pas directement liée au comportement en cas de panne.

---

## TIER 3 — DIFFICILE (Pièges de prof, distinctions subtiles)

---

**Q13. Un étudiant affirme : "Le proxy transparent ne nécessite aucune configuration côté client ET tous les clients web doivent être configurés manuellement." Cette affirmation est :**

A) Entièrement vraie  
B) Entièrement fausse  
C) Partiellement vraie — la première partie est vraie, la seconde est fausse  
D) Partiellement vraie — la première partie est fausse, la seconde est vraie  

✅ **Réponse : C**

**Pourquoi C est correct :** Le proxy transparent ne nécessite EFFECTIVEMENT aucune configuration côté client (première partie vraie). Cependant, c'est le proxy NON-transparent qui nécessite que chaque programme client soit configuré manuellement — pas le transparent. La seconde affirmation est fausse et correspond au proxy non-transparent (deuxième partie fausse).

**Pourquoi A est faux :** La seconde partie est incorrecte — elle décrit le proxy non-transparent, pas le transparent.

**Pourquoi B est faux :** La première partie est correcte.

**Pourquoi D est faux :** C'est l'inverse — la première partie est vraie, la seconde est fausse.

---

**Q14. Quelle affirmation sur SOCKS5 est INCORRECTE ?**

A) SOCKS5 supporte l'authentification par nom d'utilisateur et mot de passe  
B) SOCKS5 est défini par l'IETF dans la RFC 1928  
C) SOCKS5 possède des capacités de mise en cache spécifiques supérieures au proxy HTTP  
D) SOCKS5 supporte les connexions UDP en plus de TCP  

✅ **Réponse : C**

**Pourquoi C est correct (réponse à l'affirmation INCORRECTE) :** SOCKS ne possède PAS de capacités de mise en cache — c'est explicitement mentionné dans le cours. C'est une limitation de SOCKS comparativement au proxy HTTP avec cache.

**Pourquoi A est fausse (l'affirmation A est vraie) :** SOCKS5 supporte bien l'authentification username/password (et GSS-API).

**Pourquoi B est fausse (l'affirmation B est vraie) :** RFC 1928 définit bien SOCKS5.

**Pourquoi D est fausse (l'affirmation D est vraie) :** SOCKS5 supporte UDP, contrairement à SOCKS4.

---

**Q15. Un proxy inverse est déployé devant trois serveurs web backend. Un client externe envoie une requête HTTPS. Quelle séquence décrit le mieux le flux avec SSL termination au niveau du proxy inverse ?**

A) Client → [TLS] → Proxy inverse → [TLS] → Backend → [TLS] → Proxy inverse → [TLS] → Client  
B) Client → [TLS] → Proxy inverse → [HTTP clair] → Backend → [HTTP clair] → Proxy inverse → [TLS] → Client  
C) Client → [HTTP clair] → Proxy inverse → [TLS] → Backend → [TLS] → Proxy inverse → [HTTP clair] → Client  
D) Client → [TLS] → Backend → [TLS] → Proxy inverse → [TLS] → Client  

✅ **Réponse : B**

**Pourquoi B est correct :** Avec SSL termination, le proxy inverse décrypte le TLS du client (connexion TLS Client↔Proxy), puis transmet la requête en clair (HTTP) vers le backend interne. La réponse remonte en HTTP clair vers le proxy, qui re-chiffre pour le client. C'est le comportement standard de SSL termination — décharge du backend.

**Pourquoi A est faux :** Ce scénario décrit SSL passthrough (le proxy ne décrypte pas) ou une configuration double-TLS avec re-chiffrement vers le backend, pas la SSL termination standard.

**Pourquoi C est faux :** Si le client envoie en HTTP clair, il n'y a pas de SSL termination côté client — le scénario est inversé.

**Pourquoi D est faux :** Dans ce scénario, le client communique directement avec le backend, ce qui contredit l'existence et le rôle du proxy inverse.

---

**Q16. Pourquoi le proxy non-transparent offre-t-il un niveau de sécurité "plus élevé" que d'autres types, bien que sa configuration soit plus complexe ?**

A) Parce qu'il chiffre automatiquement toutes les connexions sans intervention de l'administrateur  
B) Parce qu'il effectue une inspection approfondie du payload et propose des services supplémentaires (annotation, transformation, réduction de protocole, filtrage d'anonymat) permettant un contrôle granulaire  
C) Parce qu'il est invisible pour les attaquants externes qui ne peuvent pas détecter son existence  
D) Parce qu'il opère à la couche réseau et bloque les attaques avant qu'elles atteignent la couche applicative  

✅ **Réponse : B**

**Pourquoi B est correct :** Le proxy non-transparent offre des services additionnels qui permettent un contrôle de sécurité granulaire — annotation de groupe (tracking d'identité), transformation de médias (blocage de types de fichiers dangereux), réduction de protocole (limitation des protocoles autorisés), filtrage d'anonymat (inspection et modification des en-têtes révélateurs). Ces capacités étendues justifient sa supériorité en termes de sécurité.

**Pourquoi A est faux :** Le proxy non-transparent ne chiffre pas automatiquement les connexions — c'est la fonction de TLS/VPN.

**Pourquoi C est faux :** Ce n'est pas le proxy NON-transparent qui est invisible — c'est le TRANSPARENT. Le non-transparent est connu du client.

**Pourquoi D est faux :** Le proxy opère à la couche 7 (applicative), pas à la couche réseau. C'est une inversion du niveau OSI.

---

**Q17. Un attaquant configure un serveur SOCKS5 public non sécurisé pour offrir un service de proxy anonyme. Quel risque direct représente cet "open proxy" pour des tiers ?**

A) L'attaquant peut intercepter et modifier le contenu HTTP car SOCKS5 inspecte le payload applicatif  
B) Des tiers peuvent utiliser ce proxy pour anonymiser des attaques, l'IP de l'attaquant apparaissant dans les logs des victimes  
C) Le serveur SOCKS5 peut décrypter les connexions TLS passant par le tunnel  
D) L'attaquant peut mettre en cache les requêtes et effectuer des attaques de type cache poisoning  

✅ **Réponse : B**

**Pourquoi B est correct :** Un open proxy SOCKS accessible publiquement permet à n'importe qui de le traverser — des acteurs malveillants peuvent l'utiliser pour anonymiser leurs attaques. La victime de l'attaque verra l'IP du proxy dans ses logs, pas l'IP réelle de l'attaquant.

**Pourquoi A est faux :** SOCKS5 est agnostique au protocole — il NE PAS inspecte le payload applicatif. Il crée un tunnel opaque sans inspecter HTTP.

**Pourquoi C est faux :** SOCKS5 effectue un tunneling TCP — il ne décrypte pas TLS. Pour décrypter TLS, il faudrait faire du SSL bumping (interception man-in-the-middle avec certificat).

**Pourquoi D est faux :** SOCKS ne dispose pas de capacités de mise en cache — c'est explicitement une limitation de SOCKS par rapport au proxy HTTP.

---

**Q18. Dans une architecture réseau typique présentant [Client interne → ? → Firewall → Internet], quel composant occupe la position "?" et pourquoi ?**

A) Un routeur, pour garantir le routage optimal vers Internet  
B) Un proxy, pour inspecter le contenu applicatif avant qu'il atteigne le firewall  
C) Un IDS, pour détecter les intrusions avant qu'elles quittent le réseau  
D) Un switch, pour gérer la segmentation du réseau interne  

✅ **Réponse : B**

**Pourquoi B est correct :** La séquence décrite dans le cours est exactement : Hôte interne → Proxy Server → Firewall → Internet. Le proxy reçoit la requête interne, la relaie au firewall qui utilise son IP publique pour communiquer avec Internet. Cette architecture permet une inspection applicative AVANT le passage par le firewall.

**Pourquoi A est faux :** Le routeur gère le routage inter-réseau mais n'inspecte pas le contenu applicatif — ce n'est pas son rôle dans cette position.

**Pourquoi C est faux :** Un IDS est un système de détection passif (ou actif pour IPS) — il n'est pas positionné comme intermédiaire de forwarding dans cette chaîne.

**Pourquoi D est faux :** Un switch opère en couche 2 (Data Link) et gère la commutation au sein d'un réseau local — il ne participe pas au forwarding vers Internet dans ce contexte.

---

*Total : 5 EASY + 7 MEDIUM + 6 HARD = 18 questions*
