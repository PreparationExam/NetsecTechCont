# CH6 — SERVEUR PROXY : CHEATSHEET (< 3 MINUTES)

---

## TOP 20 POINTS HAUTE PROBABILITÉ EXAMEN

1. **Définition** : Intermédiaire entre client et serveur réel — reçoit, inspecte, reconstruit, retransmet. Double terminaison TCP.
2. **Couche OSI** : Proxy = couche 7 (Application) | Filtre de paquets = couche 3-4 (Réseau/Transport)
3. **Proxy examine** : Charge utile (payload) complète | Filtre de paquets examine : en-têtes uniquement
4. **Panne proxy** → FAIL-CLOSED : toutes communications s'arrêtent | Panne filtre → FAIL-OPEN : paquets traversent librement ⚠️
5. **Proxy transparent** : Client INCONSCIENT, aucune config client, redirection automatique au niveau réseau
6. **Proxy non-transparent** : Client CONSCIENT, config requise par programme, plus sécurisé, services additionnels (4 : annotation groupe, transformation média, réduction protocole, filtrage anonymat)
7. **SOCKS** : Norme IETF, port **1080**, agnostique au protocole, couche session (5), pas de cache HTTP, SOCKS5 = auth + UDP
8. **SOCKS vs HTTP proxy** : SOCKS = tunnel générique / HTTP proxy = inspection applicative HTTP
9. **Proxy anonyme** : N'envoie pas l'IP du client, masque `X-Forwarded-For`, contournement censure peut être **illégal**
10. **Proxy inverse (Reverse)** : Côté serveur, client INCONSCIENT, protège le serveur (≠ proxy direct qui protège le client)
11. **Proxy inverse fonctions** : Load balancing, SSL termination, caching, compression, protection DDoS
12. **Reconstruction du paquet** : Le proxy reconstruit avec **nouvelle IP source** (son IP) → masquage topologie interne
13. **Squid** : Proxy cache open-source, port **3128**, supporte HTTP/HTTPS/FTP, intègre SquidGuard pour filtrage
14. **Logs proxy** = logs applicatifs complets | Logs filtre de paquets = logs d'en-têtes uniquement
15. **SOCKS composants** : (1) serveur SOCKS, (2) programme client (FTP/Telnet/navigateur), (3) bibliothèque cliente
16. **PAC (Proxy Auto-Configuration)** : Fichier script pour configurer automatiquement les clients → proxy non-transparent
17. **IP privées** (RFC 1918) : 10.x.x.x, 172.16-31.x.x, 192.168.x.x → non routables sur Internet → nécessitent proxy/NAT
18. **Limitation principale** : SPOF (Single Point of Failure) → nécessite haute disponibilité (clustering)
19. **Open proxy** : Proxy mal configuré accessible par n'importe qui → exploitable pour attaques anonymisées
20. **Piège lexical** : "Proxy" = forward proxy (côté client) | "Reverse proxy" = côté serveur — inverser les deux = 0 point

---

## TABLEAU DE COMPARAISON ULTRA-RAPIDE

| | Transparent | Non-Transparent | SOCKS | Anonyme | Inverse |
|-|:-----------:|:---------------:|:-----:|:-------:|:-------:|
| Client conscient | ❌ | ✅ | ✅ | ❌/✅ | ❌ |
| Config client | ❌ | ✅ | ✅ | Variable | ❌ |
| Cache HTTP | ✅ | ✅ | ❌ | Variable | ✅ |
| Protège | Client | Client | Client | Client | **Serveur** |
| Agnostique protocole | ❌ | ❌ | ✅ | ❌ | ❌ |

---

## PROXY vs FILTRE DE PAQUETS — TABLE EXPRESS

| | Proxy | Filtre de Paquets |
|-|:-----:|:-----------------:|
| OSI | **7** | **3-4** |
| Examine | **Payload** | **En-tête** |
| Logs | **Complets** | **En-têtes** |
| Action | **Reconstruit** | **Autorise/Bloque** |
| Panne | **Fail-CLOSED** ✅ | **Fail-OPEN** ⚠️ |

---

## CHIFFRES ET STANDARDS CLÉS

| Élément | Valeur |
|---------|--------|
| Port SOCKS (défaut) | **1080** |
| Port Squid (défaut) | **3128** |
| Port proxy générique | **8080** |
| Standard SOCKS5 | **RFC 1928** |
| Organisation SOCKS | **IETF** |
| Protocoles Squid | **HTTP, HTTPS, FTP** |
| IP privées RFC 1918 | **10.x, 172.16-31.x, 192.168.x** |

---

## PIÈGES D'EXAMEN CRITIQUES ⚠️

- **"Le proxy transparent nécessite une config manuelle"** → FAUX. C'est le NON-transparent.
- **"SOCKS a un cache HTTP"** → FAUX. SOCKS n'a pas de cache.
- **"Le reverse proxy protège le client"** → FAUX. Il protège le **serveur**.
- **"Proxy = NAT"** → FAUX. Proxy = couche 7. NAT = couche 3.
- **"Panne proxy = paquets passent"** → FAUX. Panne proxy = tout s'arrête (fail-closed).
- **"SOCKS comprend HTTP"** → FAUX. SOCKS est agnostique au protocole applicatif.
