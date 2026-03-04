# CH3 CHEATSHEET — Firewalls (< 3 min read)

---

## TOP 20 BULLETS

1. **Pare-feu** = logiciel, matériel, ou les deux — filtre trafic entrant/sortant selon règles — critères : IP src/dst, ports, protocole, type de trafic
2. **Matériel** (pare-feu matériel) : dédié, propre OS, vitesse élevée — ex: Cisco ASA, FortiGate — coûteux, complexe
3. **Logiciel** (pare-feu logiciel) : filtre par hôte individuel — ex: Norton, McAfee — moins cher, consomme ressources système
4. **Hôte** (pare-feu basé sur l'hôte) : sur chaque machine — ex: iptables, UFW, Windows FW — portable avec VM, scalabilité limitée
5. **Réseau** (pare-feu basé sur le réseau) : protège tout le LAN — ex: pfSense, Smoothwall — scalable, ne protège pas intra-VLAN
6. **Externe** (pare-feu externe) : protège entre réseau privé et public + appareils legacy — ex: Floodgate Defender
7. **Interne** (pare-feu interne) : entre segments internes — bloque mouvement latéral — nécessite sous-réseaux supplémentaires
8. **Meilleure pratique** : combiner pare-feu matériel + logiciel, et pare-feux externes + internes
9. **Filtrage de paquets** = L3, SANS ÉTAT, header seulement — pas de mémoire de session — le plus rapide, le moins sécurisé
10. **Passerelle de niveau circuit** = L5 (Session) — valide handshake TCP uniquement — masque IP interne — ne vérifie PAS le contenu
11. **Passerelle de niveau application** = L7 — inspecte contenu applicatif — filtre GET/POST, commandes FTP — le plus lent — nécessite proxy par service
12. **Inspection multi-couches avec état** = L3-L7 — table d'état — combine filtrage paquets + applicatif — Cisco ASA
13. **NGFW** = L3-L7 + DPI + IPS intégré + contrôle applicatif + identité utilisateur + inspection trafic chiffré
14. **NAT** = traduit IP privées ↔ publiques — masque structure interne — NAPT = plusieurs internes partagent une IP publique (PAT)
15. **VPN** = encapsulation + chiffrement — opère hors du périmètre de filtrage — étapes: chiffrer → encapsuler → envoyer → désencapsuler → vérifier intégrité → déchiffrer
16. **ACL Standard** = filtre sur IP SOURCE uniquement — ACL Étendue = IP src + IP dst + MAC + ports
17. **ACLs** : évaluées de haut en bas — règles les plus spécifiques EN PREMIER
18. **Chaînes iptables** : INPUT (entrant) / FORWARD (transfert) / OUTPUT (sortant)
19. **Limites du pare-feu** : ne protège PAS contre : portes dérobées, attaques internes, ingénierie sociale, zero-day, trafic tunnelisé, mauvais mdp, attaques via ports courants
20. **5 phases de déploiement** : Planification → Configuration → Tests → Déploiement → Gestion/Maintenance

---

## TABLEAUX COMPARATIFS

### Technologies de pare-feu par couche OSI

| Technologie | Couche | Avec état | Inspecte contenu | Vitesse |
|-------------|--------|----------|-----------------|---------|
| Filtrage de paquets | L3-L4 | ❌ | ❌ | ⚡⚡⚡ |
| Niveau circuit | L5 | Session | ❌ | ⚡⚡ |
| Passerelle application | L7 | ✅ | ✅ (cmds app) | ⚡ |
| Inspection avec état | L3-L7 | ✅ | ✅ | ⚡⚡ |
| Proxy applicatif | L7 | ✅ | ✅✅ | ⚡ |
| NGFW | L3-L7 | ✅ | DPI | Variable |

### Types de NAT

| Type | Mécanisme | Efficacité IP |
|------|-----------|--------------|
| 1:1 Statique | 1 IP interne = 1 IP externe | Aucune économie |
| Dynamique | IP externe assignée dynamiquement | Limitée |
| NAPT / PAT | Plusieurs internes, 1 IP + ports | Max (le plus courant) |
| Redirection port (DNAT) | Redirige vers IP interne | — |

### Types de pare-feu par déploiement

| Type | Protège | Exemple |
|------|---------|---------|
| Matériel | Périmètre réseau | Cisco ASA, FortiGate |
| Logiciel | Hôte individuel | Norton, McAfee |
| Basé sur l'hôte | Machine locale | iptables, UFW |
| Basé sur le réseau | LAN entier | pfSense, Smoothwall |
| Externe | Entre privé/public | Floodgate Defender |
| Interne | Entre segments | — |

---

## CHIFFRES CLÉS, NOMS, STANDARDS

- **Cisco ASA** = implémentation inspection multi-couches avec état
- **pfSense** = basé FreeBSD, interface web, open-source
- **iptables** = Linux, préinstallé, commande: `sudo iptables -L -n -v`
- **UFW** = interface simplifiée iptables — défaut: désactivé — `sudo ufw default deny incoming`
- **VPN** présent à TOUTES les couches OSI (2 à 7)
- **NAT** = couche 3 (réseau)
- **NAPT** = Translation d'adresses réseau et de ports (= PAT = surcharge NAT)
- **DPI** = Inspection profonde de paquets (NGFW)

---

## ⚠️ PIÈGES À L'EXAMEN

- Filtrage de paquets = SANS ÉTAT (chaque paquet indépendant)
- Niveau circuit = valide HANDSHAKE seulement, PAS le contenu
- Passerelle application ≠ Niveau circuit : même si les deux masquent l'IP interne
- ACL standard = IP SOURCE seulement (pas destination)
- NAT interfère avec IPsec (IPsec vérifie l'intégrité des IPs que NAT modifie)
- Pare-feu ne remplace PAS antivirus
- Pare-feu ne protège PAS contre attaques internes
- NGFW = 3ème génération (troisième génération)
- VPN effectue chiffrement HORS du périmètre de filtrage de paquets
- Meilleure pratique = pare-feu matériel + logiciel en combinaison
