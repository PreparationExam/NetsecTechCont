# CH4 — BANQUE QCM IDS/IPS

---

## NIVEAU FACILE — 5 QUESTIONS (Rappel direct, définitions)

---

**Q1.** Quelle est la principale caractéristique qui distingue un IDS d'un IPS ?

A) L'IDS analyse uniquement le trafic entrant, l'IPS analyse le trafic entrant et sortant
B) L'IDS est déployé en ligne dans le chemin du trafic, l'IPS en parallèle
C) L'IDS détecte les intrusions et alerte sans bloquer, l'IPS peut bloquer le trafic en temps réel
D) L'IDS est un logiciel, l'IPS est un dispositif matériel

**✅ Réponse : C**

**Pourquoi C est correct :** La différence fondamentale est comportementale et positionnelle. L'IDS surveille passivement et alerte, sans intervenir sur le flux de données. L'IPS, déployé in-line, peut intercepter et bloquer le trafic malveillant en temps réel.

**Pourquoi A est faux :** Les deux analysent le trafic entrant ET sortant — cette distinction n'existe pas.

**Pourquoi B est faux :** C'est l'inverse. L'IPS est en ligne (in-line), l'IDS est en parallèle (out-of-band via network tap).

**Pourquoi D est faux :** Les deux peuvent être logiciels ou matériels. Ce n'est pas un critère de distinction.

---

**Q2.** Quelle méthode de détection IDS utilise une base de données de patterns d'attaques connus pour comparer le trafic ?

A) Détection par anomalies
B) Analyse d'état du protocole
C) Détection comportementale
D) Reconnaissance par signatures

**✅ Réponse : D**

**Pourquoi D est correct :** La reconnaissance par signatures (signature-based ou misuse detection) est précisément définie par la comparaison du trafic à une base de signatures d'intrusions connues via du pattern matching.

**Pourquoi A est faux :** La détection par anomalies compare le trafic à une baseline comportementale normale, pas à une base de signatures.

**Pourquoi B est faux :** L'analyse d'état du protocole compare le trafic aux spécifications RFC des protocoles, pas aux signatures d'attaques.

**Pourquoi C est faux :** "Détection comportementale" est un synonyme de détection par anomalies — ce n'est pas la méthode basée sur les signatures.

---

**Q3.** Un IDS est installé sur un hôte spécifique pour surveiller ses logs, processus et accès aux fichiers. Comment appelle-t-on ce type d'IDS ?

A) NIDS (Network-based IDS)
B) IDS Hybride
C) HIDS (Host-based IDS)
D) IDS Distribué

**✅ Réponse : C**

**Pourquoi C est correct :** Le HIDS (Host Intrusion Detection System) est installé sur un hôte spécifique et surveille son activité locale : logs, processus, applications, accès aux fichiers.

**Pourquoi A est faux :** Un NIDS surveille le trafic réseau sur un segment ou l'ensemble du réseau, pas un hôte individuel.

**Pourquoi B est faux :** L'IDS hybride combine NIDS + HIDS, ce n'est pas un déploiement sur un seul hôte.

**Pourquoi D est faux :** Un IDS distribué décrit l'architecture de déploiement (plusieurs IDS sur un réseau), pas le type de système protégé.

---

**Q4.** Parmi les affirmations suivantes, laquelle décrit correctement un vrai négatif (True Negative) dans le contexte des alertes IDS ?

A) L'IDS déclenche une alerte alors qu'une attaque réelle est en cours
B) L'IDS ne déclenche pas d'alerte alors qu'une attaque réelle est en cours
C) L'IDS déclenche une alerte alors qu'aucune attaque n'a lieu
D) L'IDS ne déclenche pas d'alerte et aucune attaque n'est en cours

**✅ Réponse : D**

**Pourquoi D est correct :** Un vrai négatif = pas d'attaque ET pas d'alerte. L'IDS identifie correctement une activité comme bénigne et ne génère pas d'alerte inutile.

**Pourquoi A est faux :** C'est la définition du vrai positif (True Positive).

**Pourquoi B est faux :** C'est la définition du faux négatif (False Negative) — la défaillance la plus dangereuse.

**Pourquoi C est faux :** C'est la définition du faux positif (False Positive).

---

**Q5.** Quels sont les 5 composants principaux d'un système IDS ?

A) Pare-feu, VPN, capteurs réseau, console de commande, base de signatures
B) Capteurs réseau, systèmes d'alerte, console de commande, système de réponse, base de signatures
C) Routeur, switch, hub, pare-feu, proxy
D) Analyseur de protocoles, SIEM, antivirus, honeypot, pare-feu

**✅ Réponse : B**

**Pourquoi B est correct :** Les 5 composants d'un IDS selon le cours sont : capteurs réseau (Network Sensors), systèmes d'alerte (Alert Systems), console de commande (Command Console), système de réponse (Response System), et base de signatures d'attaque (Attack Signature Database).

**Pourquoi A est faux :** Le pare-feu et le VPN sont des dispositifs de sécurité distincts, pas des composants internes de l'IDS.

**Pourquoi C est faux :** Ce sont des composants d'infrastructure réseau générale, pas d'un IDS.

**Pourquoi D est faux :** Le SIEM, l'antivirus et le honeypot sont des systèmes complémentaires à l'IDS, pas ses composants internes.

---

## NIVEAU MOYEN — 7 QUESTIONS (Scénarios, mécanismes)

---

**Q6.** Un administrateur réseau constate que son IDS génère des centaines d'alertes par jour pour des connexions FTP normales depuis des utilisateurs légitimes. Quel phénomène décrit cette situation et quelle en est la conséquence principale ?

A) Des vrais positifs — le système détecte correctement des attaques FTP
B) Des faux négatifs — l'IDS rate des attaques réelles
C) Des faux positifs — risque d'alert fatigue réduisant la réactivité de l'équipe
D) Des vrais négatifs — l'IDS fonctionne parfaitement

**✅ Réponse : C**

**Pourquoi C est correct :** Des connexions légitimes déclenchant des alertes = faux positifs. La conséquence directe est l'alert fatigue : les administrateurs, submergés d'alertes non pertinentes, deviennent moins réactifs et risquent de manquer de vraies attaques.

**Pourquoi A est faux :** Les vrais positifs correspondent à de vraies attaques correctement détectées. Ici il s'agit de connexions légitimes.

**Pourquoi B est faux :** Les faux négatifs = attaques non détectées. L'IDS génère ici trop d'alertes, pas trop peu.

**Pourquoi D est faux :** Un vrai négatif = activité légitime sans alerte. Ici l'alerte est bien déclenchée sur activité légitime = faux positif.

---

**Q7.** Un NIDS est déployé à l'emplacement 1 (derrière le pare-feu externe dans la DMZ). Quels avantages offre spécifiquement cet emplacement ?

A) Permet d'identifier le nombre total d'attaques venant d'Internet avant filtrage par le pare-feu
B) Surveille les attaques externes, révèle les failles du pare-feu, détecte attaques sur serveurs DMZ et surveille le trafic sortant d'un serveur compromis
C) Se concentre uniquement sur la protection des sous-réseaux critiques internes
D) Inspecte uniquement le trafic sur les dorsales réseau principales

**✅ Réponse : B**

**Pourquoi B est correct :** L'emplacement 1 (derrière pare-feu dans DMZ) offre 4 avantages : surveille les attaques externes, révèle l'incapacité éventuelle du pare-feu à bloquer certaines attaques, détecte les attaques visant les serveurs Web/FTP en DMZ, et surveille le trafic sortant généré par un serveur compromis.

**Pourquoi A est faux :** Identifier les attaques avant filtrage = emplacement 2 (à l'extérieur du pare-feu). L'emplacement 1 est après le pare-feu.

**Pourquoi C est faux :** La protection des sous-réseaux critiques correspond à l'emplacement 4.

**Pourquoi D est faux :** L'inspection des dorsales réseau correspond à l'emplacement 3.

---

**Q8.** Lors du traitement d'un paquet par un IDS utilisant le pipeline de détection en 3 étapes, dans quel ordre sont effectuées les vérifications ?

A) Stateful Protocol Analysis → Détection d'anomalies → Reconnaissance par signatures
B) Détection d'anomalies → Reconnaissance par signatures → Stateful Protocol Analysis
C) Reconnaissance par signatures → Détection d'anomalies → Stateful Protocol Analysis
D) Reconnaissance par signatures → Stateful Protocol Analysis → Détection d'anomalies

**✅ Réponse : C**

**Pourquoi C est correct :** Selon le pipeline présenté dans le cours : d'abord la comparaison des signatures (la plus rapide et précise), puis si aucune correspondance la détection d'anomalies (comparaison comportementale), puis si toujours aucune correspondance l'analyse d'état du protocole.

**Pourquoi A est faux :** L'analyse d'état du protocole est la dernière étape, pas la première.

**Pourquoi B est faux :** La détection d'anomalies est la deuxième étape, pas la première.

**Pourquoi D est faux :** L'analyse d'état du protocole est en dernière position, pas avant la détection d'anomalies.

---

**Q9.** Une organisation déploie un IDS hybride. Quel avantage majeur obtient-elle par rapport à un déploiement NIDS seul dans un environnement réseau utilisant TLS/SSL ?

A) Le NIDS détecte mieux les intrusions dans le trafic chiffré grâce à l'analyse de protocole
B) Le HIDS composant de l'IDS hybride peut analyser le trafic après déchiffrement sur l'hôte
C) L'IDS hybride désactive le chiffrement TLS pour permettre l'inspection
D) L'IDS hybride utilise des certificats numériques pour déchiffrer le trafic en transit

**✅ Réponse : B**

**Pourquoi B est correct :** Un NIDS ne peut pas inspecter le contenu d'un trafic chiffré TLS/SSL car il voit uniquement des données chiffrées. Dans un IDS hybride, le HIDS installé sur l'hôte de destination analyse le trafic après que l'application l'ait déchiffré, permettant ainsi la détection d'intrusions dans du trafic qui était chiffré en transit.

**Pourquoi A est faux :** L'analyse de protocole (stateful protocol analysis) permet de détecter des déviations du protocole TLS lui-même (ex: handshake anormal), mais pas d'inspecter le contenu chiffré.

**Pourquoi C est faux :** Un IDS ne modifie jamais le chiffrement. Cela décrirait un man-in-the-middle, pas un IDS.

**Pourquoi D est faux :** Un IDS n'a pas accès aux clés privées des serveurs et ne déchiffre pas le trafic en transit.

---

**Q10.** Un IDS actif détecte automatiquement une attaque et bloque le trafic malveillant sans intervention humaine. Quel risque principal ce mode présente-t-il ?

A) Il génère plus de faux négatifs qu'un IDS passif
B) Des faux positifs peuvent provoquer le blocage de trafic légitime, causant une interruption de service
C) Il ne peut pas envoyer d'alertes à l'administrateur
D) Il est incompatible avec une architecture NIDS

**✅ Réponse : B**

**Pourquoi B est correct :** Un IDS actif configuré pour bloquer automatiquement est soumis au risque des faux positifs. Si l'IDS identifie incorrectement du trafic légitime comme malveillant, il le bloque automatiquement, créant potentiellement une interruption de service — un risque similaire à celui d'un IPS mal configuré.

**Pourquoi A est faux :** Le mode actif vs passif n'influence pas directement le taux de faux négatifs. Les faux négatifs dépendent de la méthode de détection, pas du mode de réponse.

**Pourquoi C est faux :** Un IDS actif envoie également des alertes — son action de blocage est additionnelle aux alertes, pas à leur place.

**Pourquoi D est faux :** Un IDS actif peut très bien être implémenté dans une architecture NIDS. De plus, un IPS est précisément un NIDS actif déployé in-line.

---

**Q11.** L'administrateur souhaite déployer la console de commande de son IDS. Sur quel type de système doit-il l'installer ?

A) Sur le pare-feu principal pour bénéficier de ses ressources matérielles puissantes
B) Sur le serveur de sauvegarde pour garantir la disponibilité des données en cas d'incident
C) Sur un système informatique dédié séparé des autres fonctions réseau
D) Sur le routeur principal pour avoir une vision globale du trafic

**✅ Réponse : C**

**Pourquoi C est correct :** La console de commande doit être installée sur un système dédié. Si elle est installée sur un pare-feu, routeur ou serveur de sauvegarde déjà occupé, la réponse aux événements de sécurité sera considérablement ralentie car ces systèmes gèrent déjà d'autres tâches critiques.

**Pourquoi A est faux :** Installer la console sur le pare-feu est explicitement déconseillé dans le cours — le pare-feu est déjà chargé de la gestion du trafic.

**Pourquoi B est faux :** Le serveur de sauvegarde est un autre système occupé — même problème de partage de ressources.

**Pourquoi D est faux :** Le routeur gère le routage du trafic et ne doit pas héberger de logiciels additionnels de sécurité.

---

**Q12.** Une organisation utilise un IDS à structure distribuée. Quel avantage principal offre cette architecture par rapport à une structure centralisée ?

A) Meilleure corrélation globale des événements de sécurité grâce à un point central
B) Administration simplifiée avec une seule console de gestion
C) Pas de point unique de défaillance et meilleure résistance aux attaques distribuées comme le DDoS
D) Coût de déploiement inférieur car un seul moteur d'analyse est nécessaire

**✅ Réponse : C**

**Pourquoi C est correct :** Un IDS distribué n'a pas de point unique de défaillance (contrairement au centralisé), offre une meilleure scalabilité, une détection rapide et locale, et résiste mieux aux attaques distribuées comme le DDoS car chaque segment du réseau est surveillé indépendamment.

**Pourquoi A est faux :** La meilleure corrélation globale est l'avantage de l'IDS CENTRALISÉ, pas distribué.

**Pourquoi B est faux :** L'administration simplifiée avec une console unique est l'avantage de l'IDS centralisé.

**Pourquoi D est faux :** Un IDS distribué a plusieurs moteurs d'analyse (un par nœud), donc coût généralement plus élevé.

---

## NIVEAU DIFFICILE — 6 QUESTIONS (Pièges de prof, cas limites)

---

**Q13.** Un IDS basé sur les anomalies génère une alerte lorsqu'un employé travaille tard le soir, ce qui dépasse la baseline d'utilisation normale du réseau. Quel phénomène illustre cette situation et quelle est son implication pour la conception de la baseline ?

A) Un faux négatif — l'IDS a raté une vraie attaque interne
B) Un vrai positif — tout comportement déviant est une attaque par définition
C) Un faux positif — démontrant que la construction d'un modèle de comportement "normal" est incomplète et spécifique à l'environnement
D) Un vrai négatif — l'IDS fonctionne correctement en ignorant ce comportement

**✅ Réponse : C**

**Pourquoi C est correct :** C'est un faux positif classique de la détection par anomalies. Le cours indique explicitement que "l'incapacité à construire un modèle complet du comportement normal sur un réseau pose problème" et que "ces modèles doivent être appliqués à un réseau précis, car chaque environnement a des comportements différents". Un employé qui travaille tard occasionnellement est un comportement légitime non capturé dans la baseline, ce qui déclenche une fausse alerte.

**Pourquoi A est faux :** Un faux négatif = attaque non détectée. Ici, l'IDS génère bien une alerte.

**Pourquoi B est faux :** Tout comportement déviant n'est pas une attaque — c'est précisément le problème de la détection par anomalies. La déviation est une condition nécessaire mais pas suffisante.

**Pourquoi D est faux :** Un vrai négatif = pas d'alerte sur activité légitime. Ici l'alerte est déclenchée sur activité légitime.

---

**Q14.** Une organisation déploie un IPS (Intrusion Prevention System). Un attaquant envoie un paquet TCP avec des flags inhabituels qui ne correspondent à aucune signature connue, mais qui dévie légèrement des spécifications RFC. Quelle méthode de détection de l'IPS serait la plus efficace pour détecter cette attaque ?

A) Reconnaissance par signatures — car les patterns TCP inhabituels sont catalogués
B) Détection par anomalies — car le volume de trafic TCP a augmenté
C) Analyse d'état du protocole — car la déviation des spécifications RFC est précisément son domaine de détection
D) Aucune méthode ne peut détecter cette attaque car la signature n'est pas connue

**✅ Réponse : C**

**Pourquoi C est correct :** L'analyse d'état du protocole est précisément conçue pour "détecter les écarts par rapport au comportement normal d'un protocole" en se basant sur des "profils prédéfinis" conformes aux "spécifications RFC". Des flags TCP qui violent les spécifications RFC tombent exactement dans son périmètre de détection.

**Pourquoi A est faux :** La reconnaissance par signatures ne peut détecter que les attaques dont la signature est dans la base. Une attaque inconnue avec flags inhabituels n'y sera pas cataloguée.

**Pourquoi B est faux :** La détection par anomalies compare des comportements statistiques globaux (volume, bande passante, taux de connexion), pas les spécifications techniques des protocoles.

**Pourquoi D est faux :** L'affirmation est incorrecte — la stateful protocol analysis peut détecter des attaques inconnues si elles violent les spécifications RFC, même sans signature préexistante.

---

**Q15.** Un administrateur configure son IDS et décide de ne surveiller que les connexions entrantes pour économiser les ressources. Quelle menace critique cette configuration ignore-t-elle ?

A) Les attaques DDoS qui saturent la bande passante entrante
B) Les scans de ports provenant de l'extérieur du réseau
C) Le trafic sortant généré par un serveur compromis (exfiltration de données, C&C communications)
D) Les tentatives d'authentification par force brute sur les services exposés

**✅ Réponse : C**

**Pourquoi C est correct :** "Surveiller uniquement les connexions entrantes" est listée explicitement comme une erreur courante de configuration. Un serveur compromis génère du trafic sortant vers un serveur de commande et contrôle (C&C), exfiltre des données, ou lance des attaques contre d'autres cibles. Sans surveillance du trafic sortant, cette activité malveillante post-compromission est invisible.

**Pourquoi A est faux :** Les DDoS saturent le trafic entrant — ils seraient détectés même avec surveillance entrante uniquement.

**Pourquoi B est faux :** Les scans de ports entrants seraient détectés par la surveillance entrante.

**Pourquoi D est faux :** Les tentatives de force brute entrantes seraient détectées par la surveillance entrante.

---

**Q16.** Quelle affirmation concernant la relation entre IDS/IPS et d'autres outils de sécurité est CORRECTE selon les limitations décrites dans le cours ?

A) Un IDS avec détection par anomalies peut remplacer un antivirus car il détecte les comportements malveillants des virus
B) Un IDS peut remplacer un scanner de vulnérabilités car il analyse les paquets réseau
C) Un IPS inline peut remplacer un pare-feu car il bloque le trafic malveillant
D) Un IDS ne peut pas remplacer un antivirus, un scanner de vulnérabilités, un système cryptographique, ni un système de journalisation réseau

**✅ Réponse : D**

**Pourquoi D est correct :** Le cours est explicite : "IDS/IPS ne peut pas être un remplaçant" de ces 4 catégories. Chaque outil a un rôle spécifique : l'antivirus traite les infections sur les hôtes, le scanner trouve les vulnérabilités existantes, les systèmes cryptographiques (VPN, SSL) sécurisent les communications, et les systèmes de journalisation réseau détectent des vulnérabilités de type DoS.

**Pourquoi A est faux :** L'IDS détecte le comportement suspect mais ne traite pas les infections virales et ne désinfecte pas les fichiers — c'est le rôle de l'antivirus.

**Pourquoi B est faux :** Un scanner de vulnérabilités (comme Nessus) cherche activement les failles dans les systèmes — l'IDS surveille passivement le trafic et ne sonde pas les systèmes.

**Pourquoi C est faux :** Un IPS ne remplace pas un pare-feu. Ils opèrent à des niveaux différents : le pare-feu filtre selon des règles réseau (couches 3-4), l'IPS fait de l'inspection de contenu profonde. Les deux sont complémentaires.

---

**Q17.** Un IDS basé sur les signatures possède une base avec 50 000 signatures. L'administrateur souhaite en ajouter 200 000 supplémentaires pour améliorer la détection. Quel impact négatif technique cette décision entraîne-t-elle directement ?

A) L'IDS ne pourra plus détecter les attaques zero-day
B) Le taux de faux négatifs augmentera significativement
C) La consommation de bande passante augmentera et des paquets pourraient être perdus en raison du temps de traitement accru
D) La console de commande ne pourra plus afficher les alertes en temps réel

**✅ Réponse : C**

**Pourquoi C est correct :** Le cours indique explicitement : "Un grand volume de signatures consomme davantage de bande passante réseau" et "une augmentation du nombre de signatures peut conduire à la perte de certains paquets". Chaque paquet doit être comparé à toutes les signatures — plus il y en a, plus le temps de traitement augmente, risquant des pertes de paquets et une dégradation des performances réseau.

**Pourquoi A est faux :** La détection zero-day est une limitation de la méthode par signatures en général, indépendamment du volume de signatures. Ajouter plus de signatures d'attaques connues ne change pas l'incapacité à détecter l'inconnu.

**Pourquoi B est faux :** Plus de signatures correctement définies réduisent théoriquement les faux négatifs (plus d'attaques connues couvertes). Mais le cours ne décrit pas cette relation directe — l'impact technique décrit est sur les performances.

**Pourquoi D est faux :** La console de commande fonctionne indépendamment de la taille de la base de signatures. La console reçoit les alertes générées, elle n'est pas affectée par le volume de signatures.

---

**Q18.** Un administrateur affirme : "Mon IDS basé sur les anomalies détecte les zero-days, donc je n'ai pas besoin de mettre à jour ma base de signatures." Cette affirmation est :

A) Correcte — la détection par anomalies remplace entièrement la détection par signatures
B) Incorrecte — la détection par anomalies génère trop de faux positifs pour être seule utilisée, et ne détecte les zero-days que comme déviation statistique, pas avec précision
C) Correcte — la mise à jour des signatures est uniquement nécessaire pour les IPS, pas les IDS
D) Incorrecte — la détection par anomalies ne peut pas détecter les zero-days, contrairement à ce que l'administrateur croit

**✅ Réponse : B**

**Pourquoi B est correct :** L'affirmation contient deux erreurs. Premièrement, la détection par anomalies génère un taux élevé de faux positifs qui la rend difficile à utiliser seule en production. Deuxièmement, même si elle détecte les zero-days comme déviations statistiques, elle ne peut pas identifier précisément la nature de l'attaque ni y répondre de manière ciblée. Les deux approches sont complémentaires — un IDS hybride (signature + anomalies) est la meilleure pratique. La mise à jour des signatures reste nécessaire pour la précision sur les attaques connues.

**Pourquoi A est faux :** Aucune méthode ne remplace entièrement l'autre. Les deux ont des forces et faiblesses distinctes et complémentaires.

**Pourquoi C est faux :** La mise à jour des signatures est nécessaire pour tout IDS/IPS utilisant la méthode par signatures, qu'il soit IDS ou IPS.

**Pourquoi D est faux :** La détection par anomalies CAN détecter les zero-days (c'est son principal avantage sur les signatures) — mais l'administrateur a tort sur la conclusion qu'il en tire (supprimer les signatures).
