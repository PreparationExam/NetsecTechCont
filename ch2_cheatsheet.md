
# CH2 CHEATSHEET — Segmentation Réseau (< 3 min read)

---

## TOP 20 BULLETS

1. **Réseau plat = vulnérable** : tout sur le même domaine de diffusion → mouvement latéral libre après breach périmétrique
2. **Segmentation = diviser** le réseau pour que les systèmes qui n'interagissent pas soient sur des segments différents
3. **5 avantages** : Sécurité renforcée / Meilleur contrôle d'accès / Surveillance améliorée / Meilleures performances / Meilleure isolation des incidents
4. **Segmentation physique** = la plus sécurisée, mais la plus COÛTEUSE et COMPLEXE — matériel dédié par segment
5. **Segmentation logique = VLANs** (IEEE 802.1Q) — pas de nouveau matériel, flexible, contrôle broadcast — risque: VLAN hopping
6. **Virtualisation réseau (NV)** = overlay sur underlay — plusieurs réseaux virtuels sur même infra physique — peut partager des espaces IP identiques
7. **Hôte Bastion** = seul système adressable directement depuis Internet vers l'intranet — 2 interfaces (publique + privée)
8. **Bastion durcissement** : pas de comptes utilisateurs, NFS désactivé, services minimaux, logs en double, checksum d'intégrité
9. **Bastion placement** = DMZ (réseau périmétrique) — PAS sur le réseau interne
10. **Victim Machine** = jetable, non réutilisable, héberge services risqués — utilisateurs ne doivent PAS se sentir à l'aise dessus
11. **Non-routing dual-homed** = routage DÉSACTIVÉ entre interfaces — c'est sa propriété de sécurité critique
12. **DMZ** = petit réseau entre réseau privé et public — hôtes DMZ ne peuvent PAS se connecter au réseau interne
13. **DMZ servers** : Web + Mail + DNS + FTP + Proxy
14. **Single Firewall DMZ** = 3 interfaces (three-legged) — SPOF — moins cher — moins sécurisé
15. **Dual Firewall DMZ** = plus sécurisé, plus complexe, pas de SPOF — recommandé en entreprise
16. **Trafic Nord-Sud** = client externe ↔ serveur datacenter (vertical) — mieux couvert par la sécurité traditionnelle
17. **Trafic Est-Ouest** = serveur ↔ serveur dans datacenter (horizontal) — **plus volumineux**, **surface d'attaque plus grande**, historiquement négligé
18. **Micro-segmentation** = granularité au niveau workload/application — combat le mouvement latéral Est-Ouest
19. **Zero Trust = protège les ACCÈS, pas le réseau** — "never trust, always verify" — technologies: MFA, PAM, micro-segmentation, chiffrement
20. **Zero Trust architecture** : Control Plane (vérifie + approuve) → Data Plane (exécute selon approbation)

---

## COMPARISON TABLES

### Segmentation Types

| Type | Isolation | Hardware | Coût | Sécurité |
|------|-----------|----------|------|----------|
| Physique | Matérielle | Dédié | Élevé | Max |
| Logique (VLAN) | L2 tags (802.1Q) | Partagé | Faible | Moyen |
| Virtualisation | Overlay software | Partagé | Moyen | Élevé |

### DMZ Architectures

| | Single FW | Dual FW |
|--|-----------|---------|
| Interfaces | 3 (three-legged) | 2 FW séparés |
| SPOF | OUI | NON |
| Sécurité | Modérée | Maximale |
| Complexité | Faible | Élevée |

### Traffic Flows

| | Nord-Sud | Est-Ouest |
|--|----------|-----------|
| Direction | Vertical | Horizontal |
| Source | Client externe | Serveur interne |
| Volume | Moins | **Plus** |
| Couverture sécurité | Forte | **Faible** (historique) |

---

## KEY NUMBERS & STANDARDS

- **IEEE 802.1Q** = standard VLAN tagging
- **Ports DMZ typiques autorisés** : 80 (HTTP), 443 (HTTPS), 25 (SMTP)
- **Interfaces Single FW DMZ** : 3 (WAN, LAN, DMZ)
- **Bastion logs** : 2 copies maintenues
- **Alerte typique bastion** : 3 échecs de connexion avec identifiants différents
- **Spine** = switch central datacenter ; **Leaf** = switch d'accès connecté aux serveurs

---

## ⚠️ EXAM TRAPS

- Physical segmentation = most SECURE, NOT most common
- VLANs = do NOT require new hardware
- Bastion ≠ firewall (it works WITH firewalls)
- Victim machines are NOT reusable
- Zero Trust protects ACCESS, not the network
- East-West traffic > North-South in volume
- DMZ hosts → Internal network: BLOCKED by default
