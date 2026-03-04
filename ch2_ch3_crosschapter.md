# ANALYSE CROISE CHAPITRE — CH2 × CH3
## (Après Jour 1 : Segmentation + Pare-feux)

---

## CONCEPTS CHEVAUCHANTS

### 1. DMZ — Apparaît dans les deux chapitres

| Aspect | Perspective Ch2 | Perspective Ch3 |
|--------|---|---|
| Qu'est-ce que c'est | Une zone de segmentation | Nécessite une architecture de pare-feu pour mettre en œuvre |
| Comment cela se crée | Technique de segmentation réseau | Pare-feu unique (trois jambes) ou double pare-feu |
| Qui le met en œuvre | Pare-feu | Filtrage de paquets, inspection avec état, ACL |
| Règles de trafic | Définit CE qui est autorisé | Pare-feu IMPOSE ce qui est autorisé |

**Combined exam question:** "Décrivez comment une DMZ est mise en œuvre en combinant les techniques de segmentation réseau et les technologies de pare-feu."

---

### 2. Hôte bastion — Ch2 le définit ; Ch3 explique comment le pare-feu le supporte

- L'hôte bastion est placé dans la DMZ (concept Ch2)
- Le pare-feu filtre le trafic VERS ET DEPUIS le bastion (mécanisme Ch3)
- Le bastion utilise les fonctions de filtrage de paquets et proxy (technologies Ch3)
- L'architecture du pare-feu (pare-feu externe + interne) entoure le placement du bastion

**Combined exam question:** "Quel type de pare-feu est le plus approprié pour protéger un hôte bastion dans une DMZ, et pourquoi ?"
→ Answer: External firewall filters incoming Internet traffic. Internal firewall protects intranet from bastion. Together = defense-in-depth.

---

### 3. Zéro Trust × Pare-feux internes

- Zéro Trust (Ch2) nécessite l'application de politiques d'accès par demande
- Les pare-feux internes (Ch3) sont le mécanisme d'application pour le trafic Est-Ouest dans les architectures Zéro Trust
- La micro-segmentation (Ch2) est mise en œuvre via pare-feux internes + ACL (Ch3)

**Combined exam question:** "Comment les pare-feux internes et les ACLs contribuent-ils à la mise en œuvre d'une architecture Zero Trust ?"

---

### 4. Segmentation VLAN × ACL

- Les VLAN (Ch2) créent une isolation de couche 2 entre segments
- Les ACL (Ch3) contrôlent le routage inter-VLAN à la couche 3/4
- Sans ACL, les VLAN fournissent uniquement la séparation du domaine de diffusion — pas d'isolation réelle de sécurité
- C'est la question combinée la plus courante : "Les VLAN seuls ne suffisent pas pour la sécurité — pourquoi et qu'est-ce qui doit être ajouté ?"

**Réponse :** Les VLAN isolent à L2 mais le routage inter-VLAN via un appareil de couche 3 (routeur ou commutateur L3) contourne l'isolation VLAN. Les ACL sur le routeur/pare-feu doivent appliquer les VLAN pouvant communiquer. Sans ACL, un hôte compromis dans un VLAN peut acheminer vers n'importe quel autre VLAN.

---

### 5. Trafic Est-Ouest × Pare-feux internes

- Le trafic Est-Ouest (Ch2) est le trafic intra-datacenter entre serveurs
- Les pare-feux internes et les pare-feux de segmentation interne (Ch3) adressent ce trafic
- La micro-segmentation (Ch2) est appliquée par les pare-feux basés sur l'hôte (Ch3) ou les pare-feux internes basés sur le réseau

---

## CONCEPTS FACILEMENT CONFONDUS ENTRE CHAPITRES

| Concept A (Ch2) | Concept B (Ch3) | Distinction clé |
|---|---|---|
| Segmentation logique (VLAN) | ACL | VLAN = isolation L2 ; ACL = application L3/L4. Les deux nécessaires pour la vraie sécurité. |
| Hôte bastion | Pare-feu | Bastion = hôte intermédiaire durci. Pare-feu = appareil de filtrage de trafic. Le bastion fonctionne AVEC le pare-feu. |
| DMZ (zone) | Architecture DMZ (config FW) | DMZ est un concept ; la DMZ avec pare-feu unique/double est la mise en œuvre. |
| Zéro Trust (modéle) | Pare-feu interne (mécanisme) | ZT est le framework de politique ; le pare-feu interne est l'outil d'application. |
| Micro-segmentation | Filtrage de paquets | Micro-segmentation = isolation granulaire L4-L7 ; Filtrage de paquets = inspection basique d'en-tête L3. |

---

## QUESTIONSEXAMENS COMBINIES HAUTE PROBABILITE

1. **"Comparez la segmentation par VLANs et la mise en place d'une DMZ avec pare-feu. Quelles limitations des VLANs la DMZ corrige-t-elle ?"**

2. **"Un attaquant compromet un serveur en DMZ. Expliquez comment la combinaison segmentation réseau + pare-feu interne limite les dommages."**

3. **"Pourquoi un réseau Zero Trust doit-il combiner la micro-segmentation (Ch2) avec des pare-feux internes à inspection stateful (Ch3) ?"**

4. **"Décrivez le traitement d'un paquet malveillant depuis Internet jusqu'au réseau interne dans une architecture à double pare-feu avec DMZ. À quel niveau chaque technologie firewall intervient-elle ?"**

---

## SYNTHsE : LA PILE DE SÉCURITÉ

```
Internet
    ↓
[Pare-feu externe / NGFW]          ← Filtrage de paquets, Avec état, NAT, Terminaison VPN
    ↓
[DMZ]                             ← Hôte bastion, Serveurs Web/Mail/DNS
    ↓
[Pare-feu interne]                ← Inspection avec état, ACL
    ↓
[Réseau interne]                  ← Segmentation VLAN, Pare-feux basés sur l'hôte
    ↓                               ACL appliquées aux règles inter-VLAN
[Ressources critiques]            ← Zéro Trust : vérifier chaque demande d'accès
```

Chaque élément de cette pile combine des concepts des deux chapitres. Une question d'examen couvrant une architecture complète puisera dans tous.
