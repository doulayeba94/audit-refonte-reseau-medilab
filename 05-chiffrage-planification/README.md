# Étape 5 — Chiffrage et planification du projet

> Chiffrage réalisé à partir du **référentiel tarifaire officiel MediLab (ANAE851)**.

## Contexte

L'architecture cible et la politique de sécurité étant validées, la direction demande un **chiffrage détaillé**, un **calcul de rentabilité (TCO/ROI)** et un **planning de déploiement** pour les 6 sites.

**Contraintes :**
- Budget CAPEX maximum : 250 000 €
- Budget OPEX maximum : 5 000 €/mois
- Délai de déploiement : 6 mois maximum (ouverture des nouveaux labos prévue à 8 mois)
- ROI attendu en moins de 3 ans
- Conformité HDS obligatoire

---

## 1. Chiffrage CAPEX détaillé

### 1.1 Rappel des sites (référentiel officiel)

| Site | Postes | Automates | Surface | Bornes Wi-Fi | Switchs accès |
|---|---|---|---|---|---|
| Siège (Paris) | 60 | 8 | 600 m² | 6 | 2 |
| Lyon | 30 | 5 | 350 m² | 4 | 1 |
| Marseille | 25 | 4 | 300 m² | 3 | 1 |
| Nouveau Labo A | 25 | 6 | 300 m² | 3 | 1 |
| Nouveau Labo B | 20 | 4 | 250 m² | 3 | 1 |
| Nouveau Labo C | 15 | 3 | 200 m² | 2 | 1 |
| **TOTAL** | **175** | **30** | **2 000 m²** | **21** | **7** |

### 1.2 Chiffrage CAPEX par site — version initiale (tarifs officiels, sans optimisation)

*(Switch distribution L3 ×2/site pour redondance HSRP/VRRP, baie de brassage neuve sur chaque site, câblage : 50% conservé sur sites existants)*

| Équipement | Siège | Lyon | Marseille | Labo A | Labo B | Labo C |
|---|---|---|---|---|---|---|
| Switchs accès L2 PoE+ (3 500 €) | 7 000 € | 3 500 € | 3 500 € | 3 500 € | 3 500 € | 3 500 € |
| Switchs distribution L3 ×2 (8 000 €) | 16 000 € | 16 000 € | 16 000 € | 16 000 € | 16 000 € | 16 000 € |
| Bornes Wi-Fi 6 (800 €) | 4 800 € | 3 200 € | 2 400 € | 2 400 € | 2 400 € | 1 600 € |
| Routeur WAN/SD-WAN (3 000 €) | 3 000 € | 3 000 € | 3 000 € | 3 000 € | 3 000 € | 3 000 € |
| Baie de brassage 42U (2 500 €) | 2 500 € | 2 500 € | 2 500 € | 2 500 € | 2 500 € | 2 500 € |
| Câblage Cat6a (150 €/prise) | 5 100 € | 2 700 € | 2 250 € | 4 650 € | 3 600 € | 2 700 € |
| **Sous-total / site** | **38 400 €** | **30 900 €** | **29 650 €** | **32 050 €** | **31 000 €** | **29 300 €** |

**Total LAN (6 sites) : 191 300 €**

### 1.3 Équipements centralisés et services (version initiale)

| Poste | Prix référentiel | Détail | Total |
|---|---|---|---|
| Pare-feu NGFW (cluster HA) | 15 000 € | 1 cluster | 15 000 € |
| Serveur NAC / RADIUS | 4 000 € | 1 unité | 4 000 € |
| Solution SIEM | 8 000 € | 1 unité | 8 000 € |
| Contrôleur Wi-Fi centralisé | 5 000 € | 1 unité | 5 000 € |
| **Sous-total sécurité/Wi-Fi centralisé** | | | **32 000 €** |
| Intégration et déploiement (20% CAPEX matériel) | 20% × 223 300 € | | 44 660 € |
| Formation administrateurs | 4 500 € | 1 session | 4 500 € |
| Formation utilisateurs | 1 500 € | 1 session | 1 500 € |
| **Sous-total avant marge** | | | **273 960 €** |
| Marge de sécurité (10%) | | | 27 396 € |
| **TOTAL CAPEX (version initiale)** | | | **301 356 €** |

### ⚠️ Le CAPEX respecte-t-il l'enveloppe de 250 000 € ?

**Non.** Le chiffrage initial dépasse le plafond de **51 356 €**. Le poste le plus lourd est la redondance systématique des switchs de distribution L3 (2 par site × 6 sites = 96 000 €), suivie de l'intégration (44 660 €, calculée en haut de fourchette à 20%).

### Pistes d'optimisation retenues

| Optimisation | Économie |
|---|---|
| Supprimer la redondance du switch distribution L3 sur les 3 nouveaux labos (montée en charge progressive, criticité moindre en phase de démarrage) — conserver la redondance uniquement sur Siège/Lyon/Marseille | − 24 000 € |
| Réutiliser la baie de brassage existante sur les 3 sites déjà équipés (Siège, Lyon, Marseille) plutôt que d'en racheter une neuve | − 7 500 € |
| Ramener le ratio d'intégration de 20% à 15% (bas de la fourchette autorisée par le référentiel : 15-25%), en négociant un forfait avec l'intégrateur | − 14 765 € |

### CAPEX optimisé

| Poste | Montant |
|---|---|
| Total LAN (optimisé) | 159 800 € |
| Équipements centralisés | 32 000 € |
| Intégration et déploiement (15%) | 28 770 € |
| Formation (admin + utilisateurs) | 6 000 € |
| Sous-total avant marge | 226 570 € |
| Marge de sécurité (10%) | 22 657 € |
| **TOTAL CAPEX OPTIMISÉ** | **249 227 €** |

**✅ Conforme** : 249 227 € < 250 000 € (marge résiduelle : 773 €, très serrée — aucune dérive tarifaire tolérable sans repasser au-dessus du plafond).

---

## 2. Calcul du TCO et du ROI

### 2.1 Chiffrage OPEX mensuel (tarifs officiels, version optimisée)

*(MPLS limité aux 2 sites au trafic le plus critique — Siège et Lyon — les 4 autres sites fonctionnant sur SD-WAN/Internet uniquement, pour respecter le plafond de 5 000 €/mois)*

| Poste OPEX | Coût mensuel | Coût annuel |
|---|---|---|
| Lien MPLS 100 Mbps (2 sites critiques × 500 €) | 1 000 € | 12 000 € |
| Accès Internet 200 Mbps SD-WAN (6 sites × 200 €) | 1 200 € | 14 400 € |
| Service SD-WAN overlay (6 sites × 100 €) | 600 € | 7 200 € |
| Maintenance matériel (8% CAPEX matériel 191 800 €/an) | 1 279 € | 15 344 € |
| Support intégrateur | 250 € | 3 000 € |
| Licence NGFW | 333 € | 4 000 € |
| Licence NAC | 100 € | 1 200 € |
| Licence SIEM | 100 € | 1 200 € |
| Formation continue équipe IT | 125 € | 1 500 € |
| **TOTAL OPEX** | **4 987 €** | **59 844 €** |

**✅ Conforme** : 4 987 €/mois < 5 000 €/mois (marge résiduelle : 13 €/mois — également très serrée).

### 2.2 Calcul du TCO sur 5 ans et du ROI

```
TCO 5 ans = CAPEX + (OPEX annuel × 5) + Renouvellement partiel année 5 (15% CAPEX matériel)

TCO 5 ans = 249 227 + (59 844 × 5) + (191 800 × 0,15)
TCO 5 ans = 249 227 + 299 220 + 28 770 = 577 217 €
```

**TCO 5 ans = 577 217 €**

```
ROI = (Gains annuels − OPEX annuel) / CAPEX × 100
ROI = (120 000 − 59 844) / 249 227 × 100 ≈ 24,1 %

Délai de retour = CAPEX / (Gains annuels − OPEX annuel)
Délai de retour = 249 227 / 60 156 ≈ 4,14 ans
```

### ⚠️ Le délai de retour respecte-t-il l'objectif de 3 ans ?

**Non.** Avec les tarifs officiels, même après optimisation pour respecter les enveloppes CAPEX/OPEX, **le délai de retour réel est d'environ 4 ans et 2 mois, contre un objectif de 3 ans.**

C'est un résultat important à assumer plutôt qu'à masquer : les contraintes budgétaires (CAPEX/OPEX) et l'objectif de ROI sont **en tension** dans ce projet — respecter les deux plafonds simultanément ne suffit pas à atteindre le ROI cible avec l'estimation de gains fournie (120 000 €/an).

### Pistes pour résorber l'écart de ROI

1. **Négocier une remise fournisseur** : le référentiel indique une variation possible de ±15% selon les fournisseurs. Une négociation à -15% sur le CAPEX matériel ramènerait le délai de retour à environ **3,5 ans** — encore au-dessus de la cible mais nettement amélioré.
2. **Réévaluer les gains annuels estimés** : les 120 000 €/an ne couvrent probablement que les gains de productivité et la réduction d'incidents. Les gains liés à l'évitement d'une sanction réglementaire HDS (non-conformité) ou d'une nouvelle panne majeure (coût d'image, arrêt d'activité) ne sont pas chiffrés ici et pourraient justifier une révision à la hausse.
3. **Lisser l'investissement sur 2 exercices budgétaires** : différer le déploiement des 3 nouveaux laboratoires de 6 à 12 mois réduirait le CAPEX initial et améliorerait le ROI à court terme, au prix d'un délai de mise en service plus long pour l'expansion.

---

## 3. Planning de déploiement

### 3.1 Diagramme de Gantt simplifié (24 semaines / 6 mois)

| Phase | S1-2 | S3-4 | S5-6 | S7-8 | S9-10 | S11-12 | S13-14 | S15-16 | S17-18 | S19-20 | S21-22 | S23-24 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| **P1 : Approvisionnement** | X | X | | | | | | | | | | |
| **P2 : Pilote** | | | X | X | | | | | | | | |
| **P3 : Recette pilote** | | | | | X | | | | | | | |
| **P4 : Sites existants** | | | | | | X | X | X | | | | |
| **P5 : Nouveaux sites** | | | | | | X | X | X | X | | | |
| **P6 : Sécurité** | | | | | | | | | X | X | | |
| **P7 : Recette finale** | | | | | | | | | | | X | |

*(P4 et P5 sont menées en parallèle par deux équipes distinctes ; P6 démarre dès que l'infrastructure LAN de chaque site est prête.)*

### 3.2 Questions

```
Chemin critique = P1 → P2 → P3 → P5 (nouveaux sites, plus long que P4) → P6 → P7
Durée du chemin critique = 4 + 4 + 2 + 8 + 4 + 2 = 22 semaines

Marge = 24 semaines (délai contractuel) − 22 semaines = 2 semaines
```

**Chemin critique : 22 semaines**, porté par la branche P5 (nouveaux sites), plus longue que P4 puisqu'elle nécessite un déploiement complet (câblage, équipements, raccordement) contrairement aux sites existants qui conservent une partie de leur infrastructure.

**Marge résiduelle : 2 semaines** sur les 24 disponibles — cohérente avec le délai contractuel de 6 mois et compatible avec l'ouverture des nouveaux laboratoires annoncée à 8 mois. Cette marge reste toutefois serrée : il est recommandé de sécuriser en priorité l'approvisionnement (délais fournisseurs souvent plus longs pour du matériel neuf sur les 3 nouveaux labos) afin de ne pas consommer cette marge dès la phase P1-P2.

---

## Livrables

- [x] Chiffrage CAPEX par site + éléments centralisés (avec pistes d'optimisation)
- [x] Chiffrage OPEX mensuel
- [x] Calcul TCO (5 ans) et ROI (avec analyse de l'écart par rapport à l'objectif)
- [x] Planning de déploiement (Gantt) avec chemin critique identifié
