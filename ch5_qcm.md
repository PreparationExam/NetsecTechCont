# CH5 — HONEYPOT : BANQUE QCM

---

## TIER 1 — FACILE (Rappel direct, définitions)

---

**Q1. Quelle propriété fondamentale caractérise un honeypot ?**

A) Il possède une valeur de production élevée et héberge des services critiques  
B) Il n'a aucune activité autorisée, aucune valeur de production, et tout trafic qui lui est destiné est suspect  
C) Il filtre activement les paquets malveillants pour protéger le réseau interne  
D) Il chiffre les communications entre les clients et les serveurs de production  

✅ **Réponse : B**

**Pourquoi B est correct :** C'est la définition académique exacte du honeypot — aucune activité autorisée, aucune valeur de production, tout trafic = sonde/attaque/compromission.

**Pourquoi A est faux :** L'inverse exact — un honeypot ne doit avoir AUCUNE valeur de production. S'il en avait, des utilisateurs légitimes l'utiliseraient, contaminant les données de détection.

**Pourquoi C est faux :** C'est la fonction d'un firewall ou IPS. Le honeypot ne filtre rien — il attire et observe.

**Pourquoi D est faux :** Le chiffrement est la fonction de TLS/VPN. Le honeypot n'a aucune fonction de chiffrement.

---

**Q2. Dans quelle zone réseau le honeypot est-il typiquement positionné selon le cours ?**

A) Sur le réseau interne (LAN), derrière le firewall  
B) Directement sur Internet, sans aucune protection  
C) Dans la DMZ, entre le firewall et le filtre de paquets  
D) Sur un VLAN dédié complètement isolé d'Internet  

✅ **Réponse : C**

**Pourquoi C est correct :** Le diagramme du cours montre explicitement le honeypot dans la DMZ, aux côtés du serveur Web, entre le Firewall (côté LAN) et le Packet Filter (côté Internet).

**Pourquoi A est faux :** Le LAN est réservé aux honeypots de production (pour détecter les menaces internes), mais la position par défaut du cours est la DMZ.

**Pourquoi B est faux :** Un honeypot directement exposé sans aucune infrastructure de monitoring serait inutile — il n'y aurait pas de mécanisme pour analyser les attaques en toute sécurité.

**Pourquoi D est faux :** Un VLAN complètement isolé d'Internet ne recevrait aucun trafic attaquant — le honeypot doit être accessible pour être efficace.

---

**Q3. Quel outil honeypot est décrit dans le cours comme un "honeypot à interaction moyenne pour Windows" ?**

A) KFSensor  
B) HoneyPy  
C) HoneyBOT  
D) ESPot  

✅ **Réponse : C**

**Pourquoi C est correct :** HoneyBOT est explicitement décrit dans le cours comme "un honeypot à interaction moyenne pour Windows" développé par Atomic Software Solutions.

**Pourquoi A est faux :** KFSensor est un autre outil honeypot pour Windows mentionné dans la liste, mais n'est pas décrit comme "interaction moyenne" dans le cours.

**Pourquoi B est faux :** HoneyPy est un honeypot Python modulaire (GitHub) — pas spécifiquement décrit comme interaction moyenne pour Windows.

**Pourquoi D est faux :** ESPot est un honeypot Elasticsearch — pas un honeypot Windows d'interaction moyenne.

---

**Q4. Quel type de honeypot est conçu spécifiquement pour piéger les robots d'indexation et les crawlers web ?**

A) Email Honeypot  
B) Spider Honeypot  
C) Spam Honeypot  
D) Malware Honeypot  

✅ **Réponse : B**

**Pourquoi B est correct :** Le Spider Honeypot est "conçu spécifiquement pour piéger les robots d'indexation et les crawlers web" — c'est sa définition exacte dans le cours.

**Pourquoi A est faux :** L'Email Honeypot utilise de fausses adresses email pour attirer les courriels malveillants — cible les spammeurs/phishers, pas les crawlers.

**Pourquoi C est faux :** Le Spam Honeypot cible les spammeurs abusant de serveurs de messagerie ouverts et proxies ouverts — pas les robots d'indexation.

**Pourquoi D est faux :** Le Malware Honeypot piège les campagnes de malwares dans l'infrastructure réseau — pas les crawlers web.

---

**Q5. Qu'est-ce qu'un Honeynet ?**

A) Un honeypot haute performance capable de simuler un OS complet  
B) Un logiciel de monitoring des honeypots existants  
C) Un réseau de plusieurs honeypots interconnectés  
D) Un honeypot spécialisé dans la détection des réseaux de botnets  

✅ **Réponse : C**

**Pourquoi C est correct :** Un honeynet est défini comme un "réseau de plusieurs honeypots" — très efficace pour déterminer l'ensemble des capacités des adversaires.

**Pourquoi A est faux :** La simulation d'un OS complet décrit un honeypot à interaction moyenne, pas un honeynet.

**Pourquoi B est faux :** Un honeynet n'est pas un logiciel de monitoring — c'est une infrastructure réseau leurre complète.

**Pourquoi D est faux :** Bien qu'un honeynet puisse détecter des botnets, ce n'est pas sa définition — c'est une infrastructure multi-honeypots, pas un outil spécialisé botnets.

---

## TIER 2 — MOYEN (Scénarios, mécanismes)

---

**Q6. Une organisation déploie un honeypot sur son réseau LAN interne, à côté de ses serveurs de production. Quel objectif spécifique ce déploiement permet-il d'atteindre, qu'un honeypot en DMZ ne pourrait pas atteindre aussi efficacement ?**

A) Analyser les malwares sophistiqués provenant d'Internet  
B) Identifier les défaillances internes et les attaquants présents à l'intérieur de l'organisation  
C) Collecter des informations sur les groupes APT internationaux  
D) Simuler l'ensemble des services réseau pour tromper les attaquants externes  

✅ **Réponse : B**

**Pourquoi B est correct :** C'est la valeur ajoutée spécifique du honeypot de production déployé en interne — il peut identifier les menaces internes (insiders malveillants) car il est accessible depuis le LAN. Un honeypot en DMZ n'est accessible que depuis Internet.

**Pourquoi A est faux :** L'analyse de malwares sophistiqués depuis Internet relève plutôt d'un honeypot de recherche à forte interaction en DMZ.

**Pourquoi C est faux :** La collecte sur les groupes APT est l'objectif des honeypots de recherche, déployés par des instituts/gouvernements/militaires — pas d'un honeypot de production standard.

**Pourquoi D est faux :** La simulation de l'ensemble des services réseau correspond à un honeypot à forte interaction — ce n'est pas l'objectif défini pour les honeypots de production.

---

**Q7. Un attaquant compromet un honeypot faiblement interactif. Quelles sont les conséquences probables pour l'attaquant et pour le défenseur ?**

A) L'attaquant peut pivoter vers le réseau interne ; le défenseur perd toutes ses données de monitoring  
B) L'attaquant obtient peu d'informations utiles ; le défenseur collecte des données de reconnaissance limitées mais sans risque de pivot significatif  
C) L'attaquant dispose d'un accès complet à l'OS ; le défenseur obtient des TTPs complets  
D) L'attaquant peut exécuter du code arbitraire ; le défenseur doit immédiatement déconnecter le honeypot  

✅ **Réponse : B**

**Pourquoi B est correct :** Un honeypot à faible interaction simule seulement un nombre limité de services — l'attaquant ne peut pas interagir profondément. Le risque de pivot est faible (peu de surface d'attaque réelle). Les données collectées sont limitées aux tentatives de connexion basiques.

**Pourquoi A est faux :** Le pivot vers le réseau interne est le risque des honeypots à forte interaction ou des honeypots mal isolés, pas du faible interaction.

**Pourquoi C est faux :** L'accès complet à l'OS est possible avec un honeypot à interaction moyenne ou forte — pas faible. Les TTPs complets viennent des niveaux supérieurs.

**Pourquoi D est faux :** L'exécution de code arbitraire est bloquée par la simulation limitée du faible interaction — c'est précisément son avantage sécuritaire.

---

**Q8. Quel type de honeypot un institut universitaire de cybersécurité devrait-il déployer pour obtenir des informations détaillées sur les techniques utilisées par des groupes d'attaquants avancés (APT) ?**

A) Honeypot de production à faible interaction  
B) Honeypot de recherche à forte interaction  
C) Spam honeypot avec serveurs de messagerie ouverts  
D) Spider honeypot avec pages web piégées  

✅ **Réponse : B**

**Pourquoi B est correct :** Les honeypots de recherche sont explicitement définis comme des honeypots à forte interaction, principalement déployés par des instituts de recherche, gouvernements ou organisations militaires pour obtenir des informations détaillées sur les actions des intrus — correspond exactement au scénario.

**Pourquoi A est faux :** Les honeypots de production à faible interaction sont déployés par des entreprises pour détecter des menaces opérationnelles — pas pour l'analyse approfondie d'APT.

**Pourquoi C est faux :** Un spam honeypot cible les spammeurs qui abusent de ressources ouvertes — pas les APT avancés.

**Pourquoi D est faux :** Un spider honeypot cible les crawlers web — pas les APT.

---

**Q9. Une entreprise constate que ses serveurs de messagerie sont utilisés comme relais pour envoyer du spam par des tiers non autorisés. Quel type de honeypot permettrait de capturer et d'étudier les acteurs responsables ?**

A) Database Honeypot  
B) Email Honeypot  
C) Spam Honeypot  
D) Malware Honeypot  

✅ **Réponse : C**

**Pourquoi C est correct :** Le Spam Honeypot cible spécifiquement les spammeurs qui abusent de ressources vulnérables telles que les **serveurs de messagerie ouverts** et les **proxies ouverts** — correspond exactement au scénario décrit.

**Pourquoi B est faux :** L'Email Honeypot utilise de fausses adresses email pour attirer les courriels malveillants entrants — il ne piège pas les spammeurs qui utilisent des relais ouverts.

**Pourquoi A est faux :** Le Database Honeypot cible les injections SQL et l'énumération de BDD — sans lien avec le spam et les relais de messagerie.

**Pourquoi D est faux :** Le Malware Honeypot piège les campagnes d'infection malware dans l'infrastructure réseau — pas les spammeurs.

---

**Q10. Pourquoi le taux de faux positifs d'un honeypot est-il théoriquement nul, contrairement à un IDS ?**

A) Parce que le honeypot utilise des algorithmes de machine learning pour distinguer le trafic légitime du malveillant  
B) Parce que le honeypot n'a aucune activité autorisée — toute interaction est par définition non autorisée et donc suspecte  
C) Parce que le honeypot est protégé par un firewall qui bloque tout le trafic légitime avant qu'il l'atteigne  
D) Parce que le honeypot analyse uniquement les signatures de malwares connus  

✅ **Réponse : B**

**Pourquoi B est correct :** C'est le raisonnement logique fondamental — puisque le honeypot n'héberge aucun service légitime et n'est connu d'aucun utilisateur autorisé, toute connexion à un honeypot est nécessairement non autorisée. Il n'existe pas de "trafic légitime" à discriminer → zéro faux positif.

**Pourquoi A est faux :** Le honeypot n'utilise pas de machine learning pour discriminer — c'est inutile puisque toute interaction est malveillante par définition.

**Pourquoi C est faux :** Si le firewall bloquait tout le trafic vers le honeypot, personne ne pourrait l'atteindre, y compris les attaquants — le honeypot serait inutile.

**Pourquoi D est faux :** Le honeypot n'analyse pas des signatures — c'est la fonction d'un IDS basé sur les signatures. Le honeypot observe les comportements.

---

**Q11. Quelle est la différence entre un "Email Honeypot" et un "Spam Honeypot" ?**

A) L'Email Honeypot cible les crawlers web ; le Spam Honeypot cible les injections SQL  
B) L'Email Honeypot utilise de fausses adresses email pour attirer les courriels malveillants ; le Spam Honeypot expose des serveurs de messagerie ouverts pour piéger les spammeurs  
C) L'Email Honeypot est déployé en interne ; le Spam Honeypot est déployé en DMZ  
D) Ce sont deux noms pour le même type de honeypot — aucune différence  

✅ **Réponse : B**

**Pourquoi B est correct :** Distinction précise du cours : Email Honeypot = fausses adresses email pour attirer les courriels malveillants (vecteur entrant) / Spam Honeypot = serveurs de messagerie ouverts + proxies ouverts pour piéger les spammeurs qui cherchent des relais (vecteur sortant).

**Pourquoi A est faux :** Les crawlers et injections SQL correspondent au Spider Honeypot et Database Honeypot respectivement.

**Pourquoi C est faux :** La distinction n'est pas de positionnement géographique dans le réseau, mais de mécanisme de tromperie et d'attaque ciblée.

**Pourquoi D est faux :** Ce sont deux types distincts avec des mécanismes et des cibles différents — les confondre serait une erreur d'exam.

---

**Q12. Un attaquant compromet un honeypot en DMZ et tente de l'utiliser comme point de départ pour attaquer le réseau interne. Comment appelle-t-on cette technique et quel est le risque principal associé au déploiement des honeypots ?**

A) Spoofing — le risque principal est que l'attaquant usurpe l'identité du honeypot  
B) Pivot (pivoting) — le risque principal est que le honeypot devienne un tremplin vers le réseau interne  
C) Scanning — le risque principal est que le honeypot génère trop de faux positifs  
D) Poisoning — le risque principal est que l'attaquant injecte de fausses données dans les logs du honeypot  

✅ **Réponse : B**

**Pourquoi B est correct :** Le pivot (lateral movement) est la technique par laquelle un attaquant utilise un système compromis comme point de départ pour attaquer d'autres systèmes. C'est le risque principal d'un honeypot mal isolé — s'il est compromis et accessible au réseau interne, il devient un tremplin.

**Pourquoi A est faux :** Le spoofing est l'usurpation d'identité (IP, MAC, etc.) — pas le mouvement latéral depuis un honeypot compromis.

**Pourquoi C est faux :** Les faux positifs sont quasi-nuls pour un honeypot par définition — et le scanning est une activité de l'attaquant, pas un risque de déploiement.

**Pourquoi D est faux :** Le log poisoning est possible mais secondaire — le risque principal est le pivot, car il met en danger l'infrastructure réelle.

---

## TIER 3 — DIFFICILE (Pièges de prof, distinctions subtiles)

---

**Q13. Laquelle de ces affirmations distingue correctement "honeypot à forte interaction" et "honeypot pur" ?**

A) Ce sont des synonymes — les deux termes désignent la même chose  
B) Le honeypot à forte interaction simule l'ensemble des services d'un réseau cible ; le honeypot pur émule le véritable réseau de production d'une organisation  
C) Le honeypot pur a une faible interaction ; le honeypot à forte interaction est le niveau le plus avancé  
D) Le honeypot à forte interaction est utilisé en production ; le honeypot pur est utilisé pour la recherche  

✅ **Réponse : B**

**Pourquoi B est correct :** Le cours distingue clairement : forte interaction = "simule l'ensemble des services et des applications d'un réseau cible" (simulation générique) / pur = "émule le véritable réseau de production d'une organisation cible" (réplique fidèle d'une infrastructure spécifique). La nuance simulation vs émulation est clé.

**Pourquoi A est faux :** Ce sont deux niveaux distincts dans la classification à 4 niveaux du cours. Les confondre est une erreur directement piégée.

**Pourquoi C est faux :** L'inversion est totale — le honeypot pur est le niveau le plus avancé/risqué, pas le moins.

**Pourquoi D est faux :** Les honeypots de recherche (forte interaction) sont utilisés par des instituts/gouvernements/militaires — la distinction production/recherche croise mais ne coïncide pas avec la distinction forte/pur.

---

**Q14. Un étudiant affirme : "Le honeypot de recherche peut être à faible interaction car son but est uniquement d'observer et non d'interagir profondément avec l'attaquant." Cette affirmation est :**

A) Vraie — l'objectif d'observation ne nécessite pas une forte interaction  
B) Fausse — les honeypots de recherche sont explicitement définis comme des honeypots à forte interaction  
C) Partiellement vraie — certains honeypots de recherche peuvent être à faible interaction  
D) Hors sujet — le niveau d'interaction ne s'applique pas aux honeypots de recherche  

✅ **Réponse : B**

**Pourquoi B est correct :** Le cours est explicite : "Ce sont des honeypots à forte interaction, principalement déployés par des instituts de recherche, des gouvernements ou des organisations militaires." C'est une définition directe, sans ambiguïté.

**Pourquoi A est faux :** La logique est inversée — pour obtenir des "informations détaillées sur les actions des intrus", il faut justement une forte interaction permettant à l'attaquant d'aller loin dans sa compromission.

**Pourquoi C est faux :** Le cours ne laisse aucune ambiguïté sur ce point — honeypot de recherche = forte interaction, point final.

**Pourquoi D est faux :** Le niveau d'interaction s'applique à tous les types de honeypots — la classification est orthogonale (conception × déploiement).

---

**Q15. Dans quel scénario un honeypot peut-il paradoxalement devenir un risque de sécurité pour l'organisation qui le déploie ?**

A) Lorsqu'il détecte trop d'attaquants et génère un volume de logs ingérable  
B) Lorsqu'il est mal isolé du réseau interne et qu'un attaquant l'utilise comme pivot pour compromettre le LAN  
C) Lorsqu'il attire trop d'attaquants et sature la bande passante réseau  
D) Lorsqu'il est configuré avec un niveau d'interaction trop faible et ne collecte pas assez de données  

✅ **Réponse : B**

**Pourquoi B est correct :** C'est le risque principal documenté dans le cours et la littérature sécurité — un honeypot compromis et mal isolé devient un tremplin (pivot) vers le réseau interne. L'attaquant, une fois dans le honeypot, tente de rebondir vers les vrais systèmes.

**Pourquoi A est faux :** Un volume de logs élevé est un problème opérationnel de gestion, pas un risque de sécurité direct pour l'organisation.

**Pourquoi C est faux :** La saturation de bande passante est un problème de performance, pas un risque de compromission. De plus, les honeypots attirent peu de trafic comparé à l'infrastructure de production.

**Pourquoi D est faux :** Une faible interaction réduit la richesse des données mais n'est pas un risque de sécurité — c'est un compromis de conception acceptable.

---

**Q16. Quel est le véritable avantage du Honeynet par rapport à un ensemble de honeypots isolés déployés séparément ?**

A) Un honeynet consomme moins de ressources matérielles qu'un ensemble de honeypots isolés  
B) Un honeynet permet d'observer le comportement de mouvement latéral de l'attaquant entre plusieurs systèmes, révélant l'ensemble de ses TTPs dans un contexte de réseau réaliste  
C) Un honeynet est invisible pour les attaquants, contrairement aux honeypots isolés qui peuvent être détectés  
D) Un honeynet peut bloquer les attaques qu'il détecte, contrairement aux honeypots isolés qui ne font qu'observer  

✅ **Réponse : B**

**Pourquoi B est correct :** La valeur du honeynet est dans l'observation du **mouvement latéral** (pivot) entre plusieurs systèmes — l'attaquant croit évoluer dans un vrai réseau et révèle l'ensemble de ses TTPs. Un honeypot isolé ne montre que l'interaction avec UN système. Le cours indique que le honeynet est "très efficace pour déterminer l'ensemble des capacités des adversaires."

**Pourquoi A est faux :** Un honeynet consomme plus de ressources qu'un honeypot isolé — c'est un réseau de plusieurs honeypots.

**Pourquoi C est faux :** La détectabilité dépend de la qualité de la configuration, pas du fait d'être un honeynet ou un honeypot isolé. Les attaquants sophistiqués peuvent détecter les deux.

**Pourquoi D est faux :** Ni un honeynet ni un honeypot ne bloquent les attaques — c'est la distinction fondamentale avec un firewall/IPS. Ils observent, jamais ils ne bloquent.

---

**Q17. Un responsable sécurité affirme : "Nous avons déployé un honeypot, donc notre réseau est protégé contre les attaques." Évaluez cette affirmation.**

A) Vraie — le honeypot attire les attaquants vers lui, les détournant de l'infrastructure réelle  
B) Partiellement vraie — le honeypot offre une protection indirecte en divertissant les attaquants  
C) Fausse — le honeypot ne protège pas directement le réseau ; il collecte des informations stratégiques sans bloquer aucune attaque  
D) Vraie — le honeypot déclenche des contre-mesures automatiques lorsqu'il détecte une attaque  

✅ **Réponse : C**

**Pourquoi C est correct :** Le cours est explicite : "Il ne protège pas directement le réseau, mais collecte des informations stratégiques." Le honeypot est un outil de Threat Intelligence, pas un mécanisme de protection. La protection est assurée par le firewall, l'IPS, le proxy — pas le honeypot.

**Pourquoi A est faux :** Bien qu'un honeypot puisse divertir certains attaquants, cela n'est pas garanti et un attaquant sophistiqué peut cibler simultanément le honeypot ET l'infrastructure réelle. Affirmer une "protection" basée sur la diversion est dangereux.

**Pourquoi B est faux :** Une "protection indirecte" est une notion trop vague et potentiellement dangereuse — elle pourrait donner un faux sentiment de sécurité. Le honeypot NE protège PAS.

**Pourquoi D est faux :** Les honeypots n'ont pas de capacité de contre-mesures automatiques — c'est la fonction d'un IPS actif. Le honeypot observe et enregistre, il ne réagit pas.

---

**Q18. Quelle affirmation sur les "Database Honeypots" est la plus précise selon le cours ?**

A) Les database honeypots protègent les bases de données réelles en absorbant les attaques SQL à leur place  
B) Les database honeypots utilisent de fausses bases de données vulnérables pour attirer et étudier les attaques liées aux BDD, telles que l'injection SQL et l'énumération de bases de données  
C) Les database honeypots sont uniquement efficaces contre les attaques NoSQL — pas contre les injections SQL classiques  
D) Les database honeypots nécessitent un honeypot pur pour fonctionner correctement  

✅ **Réponse : B**

**Pourquoi B est correct :** C'est la définition exacte du cours : "Utilisent de fausses bases de données vulnérables pour permettre la réalisation d'attaques liées aux bases de données, telles que : injection SQL, énumération de bases de données."

**Pourquoi A est faux :** Le database honeypot ne protège pas la vraie BDD — il s'agit d'une fausse BDD distincte. La vraie BDD est protégée par ses propres mécanismes (WAF, chiffrement, contrôles d'accès).

**Pourquoi C est faux :** Le cours mentionne spécifiquement l'injection SQL (SQL classique) — pas seulement NoSQL.

**Pourquoi D est faux :** Un database honeypot peut fonctionner à n'importe quel niveau d'interaction — il n'y a pas de dépendance au honeypot pur.

---

*Total : 5 EASY + 7 MEDIUM + 6 HARD = 18 questions*
