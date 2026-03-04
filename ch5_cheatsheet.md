# CH5 — HONEYPOT : CHEATSHEET (< 3 MINUTES)

---

## TOP 20 POINTS HAUTE PROBABILITÉ EXAMEN

1. **Définition** : Ressource SI délibérément vulnérable, aucune activité autorisée, aucune valeur de production — tout trafic = sonde/attaque/compromission
2. **Objectif fondamental** : Attirer, observer, analyser — PAS bloquer, PAS protéger directement
3. **Zéro faux positif** : Toute interaction = malveillante par définition (aucun utilisateur légitime n'a de raison d'y accéder)
4. **Position architecturale** : En **DMZ**, entre Firewall et Packet Filter — accessible depuis Internet, isolé du LAN
5. **Risque principal** : Si mal isolé → **pivot** vers le réseau interne (honeypot = tremplin potentiel)
6. **Faible interaction** : Simule un nombre **limité** de services/applications — risque faible, données limitées
7. **Interaction moyenne** : Simule un **véritable OS** + applications/services — HoneyBOT est interaction moyenne
8. **Forte interaction** : Simule **l'ensemble** des services/applications — risque élevé, données riches, usage recherche
9. **Honeypot pur** : **Émule** (≠ simule) le véritable réseau de production — niveau maximal
10. **Production vs Recherche** : Production = réseau de prod interne, détecte menaces internes | Recherche = forte interaction, instituts/gouvernements/militaires
11. **Menaces internes** : Seul le honeypot de production peut les détecter — positionné sur le LAN
12. **Honeynet** : RÉSEAU de plusieurs honeypots — le plus efficace pour déterminer **l'ensemble des capacités adversariales**
13. **Malware Honeypot** : Piège les campagnes de malwares dans l'infrastructure réseau
14. **Database Honeypot** : Fausses BDD → injection SQL + énumération de BDD
15. **Spam Honeypot** : Cible spammeurs abusant de **serveurs de messagerie ouverts** et **proxies ouverts**
16. **Email Honeypot** : Fausses adresses email → attire les courriels malveillants
17. **Spider Honeypot** : Piège les **robots d'indexation (crawlers)** et scrapers web
18. **HoneyBOT** : Outil Windows, interaction **moyenne**, développé par Atomic Software Solutions
19. **Honeypot ≠ IDS** : IDS détecte sur trafic réel (faux positifs élevés) | Honeypot observe sur trafic 100% malveillant
20. **Honeypot ≠ Firewall** : Le firewall bloque, le honeypot **laisse entrer volontairement** pour observer

---

## TABLEAU COMPARATIF NIVEAUX D'INTERACTION

| Niveau | Simule | Risque | Données | Exemple |
|--------|--------|--------|---------|---------|
| **Faible** | Services limités | Faible | Limités | Détection basique |
| **Moyen** | OS réel + services | Moyen | Modérés | **HoneyBOT** |
| **Fort** | Tous services réseau | Élevé | Riches | Recherche avancée |
| **Pur** | Réseau prod réel | Très élevé | Maximaux | Émulation complète |

---

## TABLEAU COMPARATIF TYPES PAR TECHNOLOGIE DE TROMPERIE

| Type | Cible | Mécanisme |
|------|-------|-----------|
| Malware | Campagnes malware | Infrastructure vulnérable |
| Database | Injection SQL, énumération | Fausses BDD vulnérables |
| Spam | Spammeurs | Serveurs mail ouverts, proxies ouverts |
| Email | Phishing, harvesting | Fausses adresses email |
| Spider | Crawlers, scrapers | Pages web piégées |
| **Honeynet** | **Tous vecteurs** | **Réseau de honeypots** |

---

## PRODUCTION vs RECHERCHE

| | Production | Recherche |
|-|:----------:|:---------:|
| Interaction | Faible/Moyen | **Forte** |
| Déployé par | Entreprises | Instituts/Gouvernements/**Militaires** |
| Position | LAN interne | Variable |
| Détecte insider | ✅ OUI | ❌ Pas l'objectif |
| Objectif | Opérationnel | Analytique |

---

## PIÈGES D'EXAMEN ⚠️

- **"Le honeypot protège directement le réseau"** → FAUX. Il collecte, il n'arrête pas.
- **"Interaction moyenne = OS complet"** → ✅ VRAI (piège par inversion : faible ≠ OS complet)
- **"Forte interaction = Honeypot pur"** → FAUX. Ce sont deux niveaux distincts.
- **"Le honeypot génère des faux positifs"** → FAUX. Quasi-nul par définition.
- **"HoneyBOT = forte interaction"** → FAUX. C'est **interaction moyenne**.
- **"Honeynet = un seul honeypot puissant"** → FAUX. C'est un **réseau** de plusieurs honeypots.
- **"Honeypot de recherche = faible interaction"** → FAUX. C'est **forte interaction**.

---

## OUTILS CLÉS

| Outil | Type/Note |
|-------|-----------|
| **HoneyBOT** | Windows, interaction **moyenne** |
| **KFSensor** | Honeypot Windows (keyfocus.net) |
| **MongoDB-HoneyProxy** | Honeypot MongoDB |
| **Modern Honey Network** | Framework honeynet (GitHub) |
| **ESPot** | Honeypot Elasticsearch |
| **HoneyPy** | Honeypot Python modulaire |
