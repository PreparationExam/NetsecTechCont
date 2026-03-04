# CH4 — CHEATSHEET IDS/IPS ⚡ (< 3 minutes)

---

## DÉFINITIONS CORE

- **IDS** = détecte + alerte, **JAMAIS bloque** (mode passif par défaut), déployé **en parallèle** via network tap
- **IPS** = extension de l'IDS, déployé **en ligne (in-line)**, **bloque en temps réel**
- **Différence clé** : IPS in-line (trafic le traverse) / IDS out-of-band (trafic ne passe pas par l'IDS)
- **IDS ≠ remplacement du pare-feu** : le pare-feu filtre, l'IDS inspecte le contenu après filtrage

---

## 3 MÉTHODES DE DÉTECTION

| Méthode | Détecte | Faux Positifs | Zero-day |
|---|---|---|---|
| **Signatures** (misuse detection) | Attaques connues | Faibles | ❌ Non |
| **Anomalies** (behavioral) | Déviations baseline | **Élevés** | ✅ Oui |
| **Stateful Protocol** | Violations RFC | Moyens | ✅ Oui |

**🔥 Le piège le plus testé :** Anomaly-based = zero-day ✅ mais faux positifs élevés. Signature-based = précis mais aveugle aux zero-day.

---

## 4 TYPES D'ALERTES

| Type | Attaque ? | Alerte ? | Danger ? |
|---|---|---|---|
| Vrai Positif | ✅ | ✅ | Non |
| Faux Positif | ❌ | ✅ | Moyen (alert fatigue) |
| **Faux Négatif** | **✅** | **❌** | **🔴 LE PLUS DANGEREUX** |
| Vrai Négatif | ❌ | ❌ | Non |

---

## CLASSIFICATION IDS — 6 AXES

1. **Approche** : Signature-based / Anomaly-based
2. **Protégé** : NIDS (réseau) / HIDS (hôte) / Hybride (réseau+hôte)
3. **Structure** : Centralisé (1 point d'analyse) / Distribué (partage d'alertes)
4. **Source données** : Audit trails (logs) / Paquets réseau
5. **Comportement** : Actif (répond automatiquement) / Passif (alerte seulement)
6. **Timing** : Temps réel (on-the-fly) / Périodique (offline, interval-based)

---

## NIDS vs HIDS vs HYBRIDE

| | NIDS | HIDS | Hybride |
|---|---|---|---|
| Protège | Réseau global | Hôte seul | Les deux |
| Voit trafic chiffré | ❌ | ✅ (déchiffré sur l'hôte) | ✅ |
| Faux positifs | Moins | Plus | Optimisé |
| Détecte zero-day | Moins bien | Mieux | Optimal |

---

## 5 COMPOSANTS D'UN IDS

1. **Capteurs réseau** → collectent le trafic (placés : passerelles Internet, entre LAN, VPN, de part/d'autre du pare-feu)
2. **Analyseur** → interprète les données des capteurs
3. **Systèmes d'alerte** → email, pop-up, SMS, son
4. **Console de commande** → **doit être sur système dédié** (ex: Sguil)
5. **Base de signatures** → **doit être mise à jour régulièrement**
6. *(bonus)* **Système de réponse** → contre-mesures, l'admin ne doit pas s'y fier uniquement

---

## 7 ÉTAPES DÉTECTION D'INTRUSION

1. Installer signatures dans la BD
2. Capteurs collectent données
3. Alerte envoyée (correspondance signature ou déviation)
4. IDS réagit (pop-up / email / auto si configuré)
5. Admin évalue les dommages
6. Escalade si vrai positif
7. Journalisation + révision → mise à jour signatures

---

## IDS N'EST PAS UN REMPLACEMENT POUR

❌ Antivirus (virus, trojans, worms)
❌ Scanner de vulnérabilités
❌ Systèmes cryptographiques (VPN, SSL, Kerberos, RADIUS)
❌ Systèmes de journalisation réseau (détection DoS sur réseau congestionné)

---

## OUTILS À CONNAÎTRE

- **Snort** = NIDS open source, pattern matching, buffer overflow / stealth port scan / OS fingerprinting
- **Suricata** = IDS + IPS + NSM + PCAP offline
- **OSSEC** = HIDS open source
- **Sguil** = Console de commande IDS

---

## PIÈGES D'EXAMEN CRITIQUES 🔥

1. **IPS = extension de l'IDS** (pas un système indépendant)
2. **Faux négatif = le plus dangereux** (pas le faux positif)
3. **IDS passif ≠ inactif** → alerte mais n'intervient pas sur le flux
4. **IDS actif ≠ IPS** → l'IDS actif répond, mais le vrai IPS est in-line
5. **Anomaly-based → faux positifs ÉLEVÉS** (contre-intuitif mais crucial)
6. **Console de commande sur système NON dédié = ralentissement**
7. **NIDS ne voit pas le trafic chiffré** → seul HIDS ou Hybride le peut
8. **L'IDS surveille les menaces internes** — le pare-feu ne le fait pas
