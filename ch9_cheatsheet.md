# CH9 — LOAD BALANCER : CHEATSHEET LAST-MINUTE (< 3 min)

---

## DÉFINITION NOYAU
**Load Balancer** = dispositif qui distribue le trafic réseau entre plusieurs serveurs → performance + haute disponibilité + protection DoS/DDoS.

---

## 20 POINTS HAUTE PROBABILITÉ EXAMEN

1. **3 rôles fondamentaux** : Distribution du trafic / Haute disponibilité / Protection DoS-DDoS
2. **Architecture** : Internet → External FW → **Load Balancer** → Internal FW → Intranet (avec DMZ côté LB)
3. **Round-Robin** = séquentiel cyclique, statique, serveurs homogènes
4. **Weighted Round-Robin** = poids définis par l'**administrateur**, serveurs hétérogènes
5. **Least Connections** = dynamique, choisit le serveur avec **le moins de connexions actives**
6. **Random Connections** = sélection aléatoire de **2** serveurs → Least Connections entre eux
7. **Least Time** = serveur au **temps de réponse le plus faible**
8. **Hash** = clé prédéfinie (IP client ou URL) → même client toujours sur même serveur
9. **Source IP** = sélection basée sur **IP source** du client
10. **Session Affinity** = suit le **cookie de session** dans les en-têtes HTTP → L7 uniquement
11. **Session Affinity** = cache mémoire LB associe cookie ↔ serveur ; indispensable pour apps **stateful**
12. **L4 LB** = décisions sur IP + Port uniquement (pas de lecture cookies)
13. **L7 LB** = décisions sur contenu HTTP (cookies, URL, headers) → Session Affinity possible
14. **Clustering LB** = VIP (Virtual IP) unique + Active LB + Passive LB en failover
15. **4 bénéfices clustering** : Haute disponibilité / Répartition charge / Tolérance pannes / Scalabilité
16. **SPOF** = Single Point of Failure — le clustering évite que le LB lui-même soit un SPOF
17. **LB vs Firewall** = rôles complémentaires, NON interchangeables (LB ≠ sécurité périmétrique)
18. **LB et DDoS** = limite le nb de requêtes/seconde → empêche saturation des serveurs
19. **Outils** : EdgeNexus (L4-L7, SSO), NGINX Plus, Azure LB, AWS ELB, Citrix ADC, Avi Vantage
20. **Weighted RR vs Least Conn** : Weighted = poids fixes admin (semi-statique) vs Least Conn = temps réel dynamique

---

## TABLEAUX DE CONFUSION RAPIDE

### Algorithmes LB

| Algorithme | Critère | Dynamique ? | Usage typique |
|------------|---------|------------|--------------|
| Round-Robin | Séquentiel | ❌ | Serveurs identiques |
| Weighted RR | Poids admin | Semi ❌ | Serveurs hétérogènes |
| Least Connections | Nb conn actives | ✅ | Sessions variables |
| Random Connections | Aléatoire + Least Conn | ✅ | Grand volume |
| Least Time | Latence | ✅ | Apps sensibles latence |
| Hash | Clé (IP/URL) | Déterministe | Persistance cache |
| Source IP | IP source | Déterministe | Persistance utilisateur |

### L4 vs L7 Load Balancing

| | L4 | L7 |
|--|----|----|
| Données | IP + Port | HTTP headers, cookies, URL |
| Session Affinity | ❌ | ✅ |
| Performance | ++ | + |
| Routing intelligent | ❌ | ✅ |

### Clustering LB : Active/Passive

| Composant | Rôle |
|-----------|------|
| VIP | IP virtuelle unique vue par les clients |
| Active LB | Traite le trafic en temps réel |
| Passive LB | En veille, prend le VIP si Active tombe |
| Web Cluster | Serveurs backend (192.168.0.x:80) |

---

## CHIFFRES ET NOMS CLÉS POUR QCM

- **Session Affinity** = cookie de session dans en-têtes HTTP
- **Random Connections** = sélection **2** serveurs aléatoires puis Least Conn
- **Weighted RR** = poids attribués par l'**administrateur**
- **EdgeNexus** = L4 à L7 + SSO + accélération apps
- **VIP** = Virtual IP (adresse virtuelle unique du cluster LB)
- **SPOF** = Single Point of Failure (ce que le clustering résout)
