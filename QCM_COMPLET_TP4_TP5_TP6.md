# 📝 QCM COMPLET - TP4, TP5, TP6 PARE-FEU

---

## SECTION 1: TP4 - PARE-FEU LOCAL (WINDOWS FIREWALL)

### NIVEAU FACILE (Basique)

**Q1: Quel est le port par défaut pour Remote Desktop (RDP)?**
A) 21
B) 80
C) 3389 ✅
D) 443

**Explication:** RDP utilise le port 3389/TCP

---

**Q2: Quel service doit être installé sur Windows Server pour servir des pages web?**
A) DHCP
B) IIS Web Server ✅
C) DNS
D) Active Directory

**Explication:** IIS = Internet Information Services, port 80 (HTTP)

---

**Q3: Quel est le port standard pour FTP?**
A) 21 ✅
B) 25
C) 110
D) 3389

**Explication:** FTP = File Transfer Protocol, port 21/TCP

---

**Q4: À quel menu accède-t-on pour installer les rôles sur Windows Server?**
A) Control Panel
B) Settings
C) Server Manager ✅
D) Device Manager

**Explication:** Server Manager → Manage → Add Roles and Features

---

**Q5: Windows Firewall utilise quelle stratégie par défaut?**
A) ALLOW all
B) BLOCK incoming, ALLOW outgoing ✅
C) BLOCK all
D) DENY everything

**Explication:** Default DENY incoming = "Default Deny" principle (sécurité)

---

### NIVEAU MOYEN (Intermédiaire)

**Q6: Quelle est la différence entre les trois règles RDP qui doivent être bloquées?**
A) Elles bloquent le même port sur différents protocoles
B) Shadow = monitoring, User Mode TCP = connexion principale, User Mode UDP = alternative ✅
C) Ce sont des alias
D) Elles bloquent différents ports

**Explication:**
- Shadow (TCP-In): Monitoring sessions
- User Mode (TCP-In): Port 3389 principal
- User Mode (UDP-In): Port 3389 alternatif (faster)

---

**Q7: Pourquoi doit-on bloquer FTP Server, FTP Passive ET FTP Secure?**
A) Ce sont le même protocole sur différents ports
B) FTP Passive = firewall traversal, FTP Secure = FTPS (variantes) ✅
C) Pour être sûr que le blocage fonctionne
D) Parce que le labo le demande

**Explication:**
- FTP Server: Mode actif (port 21)
- FTP Passive: Mode passif (ports éphémères)
- FTP Secure: FTP over SSL (sécurisé mais à bloquer si politique FTP=denied)

---

**Q8: Si on active "Use recommended settings" dans Windows Firewall, que se passe-t-il?**
A) Tout trafic est permis
B) Tous les ports sont bloqués
C) Règles par défaut s'activent (BLOCK incoming) ✅
D) Le firewall se désactive

**Explication:** Recommended = strict defaults pour sécurité

---

**Q9: Dans le labo TP4, pourquoi teste-t-on RDP AVANT de bloquer?**
A) Pour vérifier que le réseau fonctionne
B) Pour montrer la connectivité avant firewall ✅
C) Pour déboguer le serveur
D) Pour faire du benchmarking

**Explication:** "Before/After" test = démonstrer l'impact du firewall rule

---

**Q10: Que signifie "This site can't be reached" lors du test RDP?**
A) Le serveur est éteint
B) Le réseau est down
C) Le firewall bloque la connexion ✅
D) La créance est écoulée

**Explication:** Erreur spécifique = firewall a rejeté la connexion

---

### NIVEAU DIFFICILE (Avancé)

**Q11: Dans Advanced Settings, pourquoi les règles RDP apareillent-elles mais sont "Disabled"?**
A) Elles ne fonctionnent pas
B) Elles sont pré-configurées mais inactives par défaut ✅
C) Le système les a supprimées
D) C'est un bug Windows

**Explication:** Windows pre-crée des règles prêtes à activer/désactiver

---

**Q12: Si on bloque RDP mais on oublie UDP-In, que se passe-t-il?**
A) TCP prend le relais
B) UDP connections passent (RDP peut utiliser UDP comme fallback) ✅
C) Rien, RDP ne fonctionne que sur TCP
D) Le labo échoue

**Explication:** RDP préfère TCP mais UDP est alternative; bloquer les deux

---

**Q13: Quelle est la différence entre "Block" et "Reject" dans Windows Firewall?**
A) Pas de différence
B) Block = silencieusement, Reject = error message ✅
C) Block = plus sûr
D) Reject = plus sûr

**Explication:**
- Block: Silently drop (timeout lent)
- Reject: "Connection refused" (feedback rapide)

---

**Q14: Dans Windows Firewall, si on configure une règle "Outbound", que contrôle-t-on?**
A) Trafic entrant au serveur
B) Trafic sortant du serveur ✅
C) Trafic inter-serveurs
D) Trafic de monitoring

**Explication:** Inbound = incoming, Outbound = outgoing

---

**Q15: Pourquoi l'ordre des règles dans Windows Firewall n'importe-t-il pas autant que dans pfSense?**
A) Windows traite tous les règles simultanément
B) Windows Firewall a un moteur différent qui évalue toutes règles ✅
C) Les deux ont le même ordre
D) L'ordre est aléatoire

**Explication:** Windows = tout-à-la-fois, pfSense = top-to-bottom (important!)

---

### NIVEAU TRÈS DIFFICILE (Tricky)

**Q16: Scénario: RDP règles sont bloquées, mais quelqu'un accède via le port 3390. Pourquoi?**
A) Le firewall a un bug
B) Il y a une autre application sur 3390 qui n'est pas bloquée ✅
C) RDP a changé de port
D) C'est impossible

**Explication:** Bloquer 3389 ≠ bloquer 3390. Chaque port spécifique.

---

**Q17: Si on crée une règle "Allow RDP from 10.10.1.11 only", où la met-on dans Advanced Settings?**
A) En bas de la liste (après les deny)
B) En haut de la liste (before the deny) ✅
C) N'importe où
D) Dans une catégorie spéciale

**Explication:** Ordre = TOP-TO-BOTTOM: specific first, general last

---

**Q18: Scénario: Admin crée une règle "Block port 3389 from 0.0.0.0/0" mais oublie de APPLY CHANGES. Que se passe-t-il?**
A) La règle s'applique automatiquement
B) La règle reste en config mais ne s'active pas ✅
C) La règle s'efface automatiquement
D) Windows Firewall crash

**Explication:** Configuration ≠ Activation. Toujours Apply!

---

**Q19: Si on désactive Windows Firewall complètement (plutôt que bloquer juste RDP), quel est le risque?**
A) Aucun risque
B) Autres malwares peuvent utiliser d'autres ports pour attaquer ✅
C) C'est plus sûr
D) Le labo demande ça

**Explication:** Defense in Depth: blocage spécifique > tout désactiver

---

**Q20: Dans TP4, pourquoi la plupart des organisations ne bloquent pas simplement TOUS les ports?**
A) C'est impossible
B) Beaucoup d'applications légitimes ont besoin d'accès réseau ✅
C) Ça ralentit trop
D) C'est contre les règles

**Explication:** Whitelist = allow specific, block rest. Blacklist = block specific, allow rest.

---

---

## SECTION 2: TP5 - PARE-FEU RÉSEAU (pfSENSE) - BLOCAGE DOMAINES

### NIVEAU FACILE

**Q21: Quel est l'IP de la passerelle pfSense dans le labo?**
A) 10.10.1.11
B) 10.10.1.16
C) 10.10.1.1 ✅
D) 10.10.1.254

**Explication:** .1 = default gateway IP dans subnet 10.10.1.0/24

---

**Q22: Qu'est-ce qu'un "Alias" dans pfSense?**
A) Un faux serveur
B) Un groupe d'IPs/domaines pour une seule règle ✅
C) Un certificat SSL
D) Une interface réseau

**Explication:** Alias = container pour plusieurs items

---

**Q23: Quelle commande teste l'IP d'un domaine?**
A) nslookup rediff.com ✅
B) ipconfig rediff.com
C) tracert rediff.com
D) netstat rediff.com

**Explication:** ping ou nslookup = domain → IP resolution

---

**Q24: Dans pfSense, où crée-t-on une alias?**
A) System → Aliases
B) Firewall → Aliases ✅
C) Services → Aliases
D) Status → Aliases

**Explication:** Firewall → Aliases → IP tab → Add

---

**Q25: Un alias de type "Host(s)" contient quoi?**
A) Seulement des IPs
B) Seulement des domaines
C) IPs et/ou domaines (FQDN) ✅
D) Seulement des subnets

**Explication:** Host(s) = flexible, accept both 84.53.185.208 and www.rediff.com

---

### NIVEAU MOYEN

**Q26: Pourquoi tester le site AVANT de créer la règle pfSense?**
A) Pour vérifier le réseau
B) Pour démontrer connectivité baseline (before state) ✅
C) Pour faire un benchmark
D) Ce n'est pas nécessaire

**Explication:** Before/After = validation que règle fonctionne

---

**Q27: Si un domaine a multiple IPs (comme rediff.com), que doit faire l'admin?**
A) Ajouter une seule IP dans alias
B) Ajouter TOUTES les IPs associées ✅
C) Bloquer seulement la première
D) Ne rien ajouter

**Explication:** Si 1 IP passe = contournement. Bloquer tous.

---

**Q28: Dans la destination d'une règle pfSense, que signifie "any"?**
A) N'importe quel port
B) N'importe quelle adresse (subnet) ✅
C) N'importe quel protocole
D) N'importe quel trafic

**Explication:** "any" = wildcard pour destination adresse

---

**Q29: Quelle est la différence entre une règle dans LAN vs WAN dans pfSense?**
A) Pas de différence
B) LAN = trafic interne, WAN = trafic internet ✅
C) LAN est plus rapide
D) WAN est plus sûr

**Explication:**
- LAN interface: protège réseau interne
- WAN interface: protège accès internet

---

**Q30: Après créer et sauvegarder une règle, quel bouton DOIT-on cliquer pour l'activer?**
A) Save (c'est déjà activé)
B) Apply Changes ✅
C) Refresh
D) Update

**Explication:** Save = write config, Apply = activate dans system

---

### NIVEAU DIFFICILE

**Q31: Scénario: Alias "BlockedWebsites" contient www.rediff.com + 84.53.185.208. Si le site change d'IP, qu'arrive-t-il?**
A) L'alias se met à jour automatiquement
B) La vieille IP est bloquée, nouvelle IP passe (contournement!) ✅
C) Les deux IPs sont bloquées
D) L'alias s'efface

**Explication:** DNS = dynamique, Admin doit update alias régulièrement

---

**Q32: Dans pfSense, si on crée une règle "Block BlockedWebsites" mais que le nom de l'alias change, que se passe-t-il?**
A) La règle continue de fonctionner
B) La règle devient invalide et ne bloque rien ✅
C) pfSense met à jour automatiquement
D) C'est impossible

**Explication:** Références d'alias = par name. Name change = broken reference

---

**Q33: Quelle est la différence entre une règle pfSense et une règle Windows Firewall concernant l'ordre de traitement?**
A) Pas de différence
B) pfSense = TOP-TO-BOTTOM (first match wins), Windows = all-at-once ✅
C) Windows = TOP-TO-BOTTOM
D) Les deux sont random

**Explication:** C'est IMPORTANT: rule order matters dans pfSense!

---

**Q34: Si on bloque un alias mais une machine utilise un proxy HTTPS vers le site, que se passe-t-il?**
A) Le site reste bloqué
B) Le site peut être accessible via proxy (contournement!) ✅
C) Impossible, le proxy aide le firewall
D) pfSense bloque aussi les proxies

**Explication:** Firewall bloque couche 3-4, pas 7 (application). Proxy = layer 7.

---

**Q35: Scénario: "Apply Changes" échoue avec erreur. Qu'est-ce qui pourrait causer ça?**
A) Alias mal nommée (spaces dans le nom)
B) IP invalide dans alias
C) Syntaxe d'adresse mauvaise
D) Tous les dessus ✅

**Explication:** Validation occur lors Apply. Check logs pour détails.

---

### NIVEAU TRÈS DIFFICILE

**Q36: Vous créez un alias avec 1000 IPs malveillantes. Quel est le risque de performance?**
A) Aucun
B) Alias n'affecte pas performance (lookup rapide)
C) La règle peut être lente si alias est très gros ✅
D) pfSense va crash

**Explication:** De très gros alias = memory overhead. Considérer plusieurs petits alias.

---

**Q37: Scénario: Règle bloque "BlockedWebsites", mais elle vient APRÈS une règle "Allow ANY". Qu'arrive-t-il?**
A) Tous les deux s'appliquent
B) Allow ANY matches first (first match wins) ✅
C) Block override Allow
D) pfSense choisit le plus strict

**Explication:** TOP-TO-BOTTOM: 1st match = decision. Allow anything BEFORE block = FAIL.

---

**Q38: Si on veut bloquer juste www.rediff.com (domaine) mais pas l'IP nue (84.53.185.208), que met-on dans l'alias?**
A) www.rediff.com seulement
B) Les deux (pas moyen de différencier IP vs domain) ✅
C) 84.53.185.208 pas dans alias
D) FQDN vs IP = traitement différent

**Explication:** Firewall bloque IP. Si domain = www.rediff.com, firewall ne connaît pas l'IP tant qu'il ne fait pas DNS.

---

**Q39: Scénario: User contourne le blocage en accédant le site via l'IP 84.53.185.208 directement (pas domain). Pourquoi ça marche?**
A) L'IP n'est pas dans alias
B) Firewall peut pas bloquer IP nue
C) DNS ne résout pas, donc IP peut passer
D) User utilise VPN ✅

**Explication:** Pas assez d'info. Si IP est dans alias = blocage. Si pas = passe.

---

**Q40: Quelle est la VRAIE raison pour laquelle les organisations utilisent des alias au lieu de créer 1 règle par domaine?**
A) C'est plus vite à configurer
B) C'est plus facile à maintenir (1 alias vs 100 règles) ✅
C) Ça économise de l'espace disque
D) pfSense limite le nombre de règles

**Explication:** Scalability + maintainability. 1 change à alias = updated everywhere.

---

---

## SECTION 3: TP6 - PARE-FEU RÉSEAU (pfSENSE) - BLOCAGE PORTS + SCHEDULES

### NIVEAU FACILE

**Q41: Quel est le port standard pour HTTP?**
A) 80 ✅
B) 443
C) 8080
D) 8443

**Explication:** HTTP = port 80 (non-sécurisé)

---

**Q42: Quel est le port standard pour HTTPS?**
A) 80
B) 443 ✅
C) 8080
D) 8443

**Explication:** HTTPS = port 443 (sécurisé, SSL/TLS)

---

**Q43: Quelle est la différence entre "Block" et "Reject" dans pfSense?**
A) Pas de différence
B) Block = silencieusement, Reject = "connection refused" ✅
C) Block = plus rapide
D) Reject = stealth mode

**Explication:**
- Block: Silently drop packets (timeout lent)
- Reject: Send RST/ICMP (feedback rapide)

---

**Q44: Si on bloque le port 80 (HTTP), le port 443 (HTTPS) fonctionne-t-il toujours?**
A) Non, tout web est bloqué
B) Oui, HTTPS sur 443 fonctionne ✅
C) Dépend du navigateur
D) Dépend de la règle

**Explication:** Règles = par-port. Bloquer 80 ≠ bloquer 443

---

**Q45: Où crée-t-on une "Schedule" (horaire) dans pfSense?**
A) System → Schedules
B) Firewall → Schedules ✅
C) Services → Schedules
D) Interfaces → Schedules

**Explication:** Firewall → Schedules → Add

---

### NIVEAU MOYEN

**Q46: Quelle est la limitation majeure d'une Schedule dans pfSense?**
A) Ne peut pas s'étendre sur 24h
B) Ne peut pas traverser minuit (doit être dans le même jour) ✅
C) Ne peut pas contenir plus de 10 jours
D) Ne peut pas être modifiée

**Explication:** Schedule = 0:00-23:59 max. Si besoin midi-demain, créer 2 schedules.

---

**Q47: Quand on crée une schedule avec jour/heure, que signifie le symbole ⭕ dans la liste?**
A) La schedule est invalide
B) La schedule est actuellement active ✅
C) La schedule a un error
D) La schedule est disabled

**Explication:** ⭕ = "in effect right now" (currently within time window)

---

**Q48: Si on associe une schedule à une règle HTTP block, que se passe-t-il pendant heures non-couverte?**
A) La règle reste active
B) La règle devient inactive (HTTP allowed) ✅
C) Impossible à dire
D) Dépend de la schedule

**Explication:** Rule + Schedule = rule only active during schedule times

---

**Q49: Scénario: Schedule = 9:00-17:00, current time = 18:30. Peut-on visiter un site HTTP?**
A) Non
B) Oui ✅
C) Dépend du jour
D) Dépend du site

**Explication:** 18:30 est en dehors 9-17, donc rule ne s'applique pas

---

**Q50: Pourquoi tester une règle port APRÈS l'avoir créée?**
A) Pour vérifier pfSense ne crash pas
B) Pour vérifier que la règle fonctionne réellement (before/after) ✅
C) Pour faire un benchmarking
D) C'est obligatoire par procédure

**Explication:** Testing = validation que règle a l'effet désiré

---

### NIVEAU DIFFICILE

**Q51: Si on crée une règle "Reject HTTP" mais pas "Allow HTTPS", que se passe-t-il?**
A) Tout web est bloqué
B) HTTP bloqué, HTTPS allowed (aucune règle = allow) ✅
C) Les deux sont bloqués
D) Impossible, HTTPS est automatiquement allowed

**Explication:** Default policy = allow outgoing (LAN can initiate connections)

---

**Q52: Scénario: Admin crée schedule 1:15-23:59. Pourquoi pas 0:00-23:59 ou 1:00-23:59?**
A) C'était le choix du labo
B) Pour avoir une "dead zone" non-couverte (testing can happen between 0-1:15) ✅
C) pfSense ne permet pas 0:00
D) C'est bug

**Explication:** Dead zone = unblocked period = test period

---

**Q53: Si on bloque HTTP et crée schedule 9-17, mais user accède via VPN depuis dehors, que se passe-t-il?**
A) VPN traverse le firewall (depends VPN config)
B) Firewall bloque VPN aussi (if no allow rule)
C) VPN fait tunnel, donc traffic intérieur peut être bloqué ✅
D) Impossible, VPN contourne tout firewall

**Explication:** VPN = tunnel, mais interior traffic still checked. Complex!

---

**Q54: Pourquoi "Reject" au lieu de "Block" pour la règle HTTP dans TP6?**
A) Reject plus rapide
B) Reject sends immediate "connection refused" vs timeout ✅
C) Block est plus sûr
D) Pas de vraie raison, les deux égaux

**Explication:** UX: Reject = fast feedback, Block = slow timeout (annoying)

---

**Q55: Scénario: Règle bloque HTTP port 80. Mais serveur écoute aussi sur port 8080 (alternative HTTP). Qu'arrive-t-il?**
A) Port 8080 est aussi bloqué
B) Port 8080 n'est pas bloqué (règle = spécifique à 80) ✅
C) Impossible, "HTTP" couvre 8080
D) Firewall décide automatiquement

**Explication:** Rules = by-port. 8080 ≠ 80. Chaque port spécifique.

---

### NIVEAU TRÈS DIFFICILE

**Q56: Si schedule ne peut pas traverser minuit, comment bloque-t-on 22:00-06:00 (overnight)?**
A) Impossible, pfSense limitation
B) Créer 2 schedules: 22:00-23:59 et 0:00-6:00, ajouter 2 règles ✅
C) Utiliser "Reject" pour minuit
D) C'est un workaround, pas solution

**Explication:** Workaround = créer 2 schedules + 2 règles pour couvrir minuit

---

**Q57: Scénario: Rule "Reject HTTP" vient AVANT règle "Allow HTTP from 10.10.1.100". Qu'arrive-t-il?**
A) Les deux s'appliquent
B) Reject matches first (top-to-bottom) ✅
C) Allow overrides Reject
D) pfSense choose intelligent policy

**Explication:** RULE ORDER = CRITICAL. 1st match wins. Specific rules BEFORE general rules!

---

**Q58: Si on associe une schedule à une règle Reject HTTP, mais l'utilisateur accède via HTTPS (443), que se passe-t-il?**
A) Schedule aussi bloque HTTPS
B) HTTPS pas affecté (port 443 ≠ 80) ✅
C) Schedule s'applique à tous ports
D) HTTPS aussi bloqué

**Explication:** Schedule + Rule = schedule applies to THAT RULE ONLY. Other rules unaffected.

---

**Q59: Quelle est la VRAIE utilité pratique d'une schedule dans un contexte entreprise?**
A) Sauver ressources serveur
B) Empêcher distractions pendant heures de travail (YouTube, etc.) ✅
C) Sauver bandwidth
D) Améliorer sécurité

**Explication:** Business continuity: block time-wasters pendant productivité, allow après heures

---

**Q60: Scénario: Admin crée schedule lundi 9-17, mais mardi 9-17 pas couverte. Est-ce correct?**
A) Oui, schedules sont par-jour
B) Non, admin doit sélectionner tous jours de travail ✅
C) pfSense auto-étend à tous jours
D) C'est configuration option

**Explication:** Click individual day vs Click day header = one day vs recurring weekday

---

---

## QUESTIONS SYNTHÈSE - COMBINER TOUS TPs

### Q61: Scénario Complet
Vous avez TP4 (host), TP5 (domain block), TP6 (port block) tous activés. User accède www.rediff.com en HTTPS (443).

**Que se passe-t-il?**
A) Complètement bloqué
B) Reachable (TP5 bloque domain mais pas 443, TP6 bloque 80 pas 443, TP4 pas HTTP block) ✅
C) Bloqué par TP4
D) Bloqué par HTTPS default

**Explication:** 
- TP5: Bloque domain
- MAIS: Domain accessible via HTTPS (443)
- TP6: Bloque port 80 (HTTP) pas 443 (HTTPS)
- TP4: Host firewall, pas règle HTTPS
= Accessible via HTTPS

---

### Q62: Defense in Depth
Les 3 TPs (TP4 host, TP5 domain, TP6 port) sont des exemples de:

A) Redundant security
B) Defense in depth (multiple layers) ✅
C) Overkill security
D) Cloud security

**Explication:** Chaque layer = défense supplémentaire. Si 1 échoue, autres protègent.

---

### Q63: Considérations Pratiques
Dans une vraie organisation, lequel est PLUS important?

A) TP4 (host firewall)
B) TP5 (domain blocking)
C) TP6 (port blocking)
D) Tous également important ✅

**Explication:** Real security = toutes couches activées. Skip une = risk.

---

### Q64: Maintenance
Si on doit mettre à jour BlockedWebsites alias avec 100 nouveaux domaines, qu'est-ce qui est PLUS efficace?

A) Créer 100 nouvelles règles individuelles
B) Ajouter 100 domaines à un alias (1 update) ✅
C) Créer 100 aliases
D) Recommencer pfSense

**Explication:** Alias = scalability. 100 items dans 1 alias vs 100 règles = huge difference.

---

### Q65: Troubleshooting
User dit "Pas d'internet depuis ce matin, mais email marche". Qu'est-ce que vous vérifiez PREMIER?

A) Network cables
B) pfSense rules (TP5/TP6) - aucune règle nouvelle? ✅
C) Host firewall (TP4)
D) ISP

**Explication:** Symptom = web down but email up = specific port block probable (port 80 rule?)

---

---

## RÉPONSES RÉSUMÉ

| Q | Réponse | Q | Réponse | Q | Réponse |
|---|---------|---|---------|---|---------|
| 1 | C | 21 | C | 41 | A |
| 2 | B | 22 | B | 42 | B |
| 3 | A | 23 | A | 43 | B |
| 4 | C | 24 | B | 44 | B |
| 5 | B | 25 | C | 45 | B |
| 6 | B | 26 | B | 46 | B |
| 7 | B | 27 | B | 47 | B |
| 8 | C | 28 | B | 48 | B |
| 9 | B | 29 | B | 49 | B |
| 10 | C | 30 | B | 50 | B |
| 11 | B | 31 | B | 51 | B |
| 12 | B | 32 | B | 52 | B |
| 13 | B | 33 | B | 53 | C |
| 14 | B | 34 | B | 54 | B |
| 15 | B | 35 | D | 55 | B |
| 16 | B | 36 | C | 56 | B |
| 17 | B | 37 | B | 57 | B |
| 18 | B | 38 | B | 58 | B |
| 19 | B | 39 | D | 59 | B |
| 20 | B | 40 | B | 60 | B |
|  |  |  |  | 61-65 | Voir explication |

---

