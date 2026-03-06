# CH7 — VPN : CHEATSHEET LAST-MINUTE (< 3 min)

---

## DÉFINITION NOYAU
**VPN** = réseau logiquement privé sur infrastructure publique → tunneling + encapsulation + chiffrement + authentification.

---

## 20 POINTS HAUTE PROBABILITÉ EXAMEN

1. **Trois fonctionnalités principales VPN** : Encapsulation / Chiffrement / Authentification
2. **Protocoles encapsulation** : PPTP, L2TP, SSH, SOCKS — ⚠️ L2TP seul ≠ chiffrement
3. **Protocoles chiffrement** : 3DES, SSL, OpenVPN
4. **Protocoles authentification** : IPSec, MS-CHAP, Kerberos
5. **Composants VPN** : Client VPN / NAS / Concentrateur VPN / Protocole VPN
6. **Fonctions concentrateur** : 8 fonctions (chiffre, authentifie, gère transfert, négocie, gère clés, établit tunnels, assigne adresses, gère flux E/S)
7. **VPN Accès Distant** = Client-to-Site → logiciel client sur poste ; passerelle VPN côté entreprise
8. **VPN Site-à-Site** = LAN-to-LAN (L2L) → Intranet (même org) ou Extranet (orgs différentes)
9. **VPN Hardware** = sécurisé + load balancing intégré + coûteux + scalabilité limitée
10. **VPN Software** = économique + moins sécurisé + charge CPU sur hôte
11. **Trusted VPN** = chemin sécurisé par opérateur (ATM/Frame Relay/MPLS) ≠ chiffrement
12. **Secure VPN** = chiffrement données ≠ contrôle chemin
13. **Hybrid VPN** = Secure VPN encapsulé dans Trusted VPN
14. **Hub-and-Spoke** = spokes communiquent via hub uniquement ; panne hub = tout tombe
15. **Star Topology** = succursales → siège uniquement, pas d'inter-succursales (banques)
16. **Full Mesh** = tunnels IPsec directs entre tous pairs → redondant mais complexe
17. **Point-to-Point** = 2 peers directs, chacun peut initier, tunnel IPsec/GRE
18. **3 apps IPsec** : Accès Distant / VPN Intranet / VPN Extranet
19. **VPN SSL** = via navigateur, sans client pré-installé, composants téléchargés dynamiquement
20. **Critères sélection VPN** : Compatibilité, Scalabilité, Sécurité (auth+chiffrement), Capacité, Coût, Besoin, Support fournisseur

---

## TABLEAUX DE CONFUSION RAPIDE

### Trusted vs Secure vs Hybrid

| | Trusted | Secure | Hybrid |
|--|---------|--------|--------|
| Chiffrement | ❌ | ✅ | ✅ |
| Contrôle chemin | ✅ | ❌ | ✅ |
| Technologies | ATM, MPLS, Frame Relay | IPsec, SSL | Les deux |

### Topologies VPN

| Topologie | Inter-branches direct | Risque panne centrale | Usage |
|-----------|----------------------|----------------------|-------|
| Hub-and-Spoke | ❌ | ✅ Élevé | Entreprise |
| Star | ❌ (interdit) | ✅ Élevé | Banques |
| Full Mesh | ✅ | ❌ Faible | Réseaux critiques |
| Point-to-Point | N/A (2 sites) | N/A | 2 datacenters |

### Hardware vs Software VPN

| | Hardware | Software |
|--|----------|---------|
| Coût | $$$ | $ |
| Sécurité | ++ | + |
| Scalabilité | Limitée | Flexible |
| Load balancing | ✅ Natif | ❌ |

---

## CHIFFRES ET NOMS CLÉS POUR QCM

- **Cisco VPN 3000** = concentrateur référence
- **OpenVPN** = port **1194** par défaut (UDP/TCP)
- **L2TP/IPsec** = L2TP encapsule, IPsec chiffre
- **MPLS** = Multiprotocol Label Switching (Trusted VPN)
- **NAS** = Network Access Server (composant VPN)
- **3DES** = 168 bits effectifs (Triple DES)
