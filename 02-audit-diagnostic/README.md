# Étape 2 — Audit et diagnostic réseau

## Contexte

Suite aux problèmes de lenteur et à une panne de 6 heures, la direction de MediLab demande un **audit technique complet** de l'infrastructure réseau, afin d'identifier les causes des dysfonctionnements et de proposer des améliorations.

## Travail réalisé

### 1. Analyse des métriques
Interprétation des données collectées lors de l'audit (métriques de supervision) et identification des goulots d'étranglement de l'infrastructure.

a) Interprétation des résultats
Nous rappelons que les résultats sont issues des collectes fournies par l'équipe IT.
| Métrique | Statut | Analyse / Cause probable |
|---|---|---|
| Latence 45ms (pic 180ms) | Problématique | Latence de base acceptable, mais le pic à 180ms en heures de pointe (4x la moyenne) indique une saturation ponctuelle du lien WAN plutôt qu'un problème de distance/routage |
| Perte paquets 0,5% | Limite | Proche du seuil acceptable (<1%) mais suffisant pour dégrader la VoIP et les sessions applicatives sensibles à la perte (cohérent avec le MOS VoIP faible) |
| Liaison WAN pics à 98% | Problématique | Lien quasiment saturé en heure de pointe (utilisation moyenne 65%, pics à 98%) — cause directe de la latence et de la perte de paquets observées |
| CPU LIMS pics à 95% | Problématique | Surcharge du serveur applicatif concentrée sur le créneau 9h-11h (heure de pointe métier) — probable sous-dimensionnement serveur ou absence de répartition de charge |
| Erreurs CRC 12/jour | Problématique | Taux d'erreurs anormal sur une interface réseau — signe probable d'un problème physique (câble défectueux, duplex mismatch) sur le port du serveur fichiers |
| VoIP MOS 3.8 | Problématique | Sous le seuil de qualité acceptable (>4.0) — cohérent avec la perte de paquets et la saturation WAN identifiées : la VoIP est la première victime de la congestion du lien |

b) Goulots d'étranglement (classés par criticité)

| Rang | Goulot identifié | Impact sur le service |
|---|---|---|
| 1 | Saturation du lien WAN (pics à 98%) | Cause racine principale : dégrade simultanément la latence, la perte de paquets et la qualité VoIP — impact transverse sur tous les usages inter-sites |
| 2 | Surcharge CPU du serveur LIMS (pics à 95% en heure de pointe) | Ralentissement direct de l'application métier critique pendant les heures d'activité les plus chargées, risque de dégradation du service labo |
| 3 | Erreurs CRC sur le port du serveur fichiers | Impact plus localisé mais révélateur d'un problème matériel non traité, pouvant évoluer vers une panne complète du lien (cohérent avec la panne de 6h déjà survenue) |

### 2. Plan de tests complémentaires
Définition des tests techniques à réaliser pour compléter le diagnostic, et proposition d'une solution de supervision à mettre en place.

### 3. Ébauche de rapport d'audit
- Synthèse des constats (documentaire, technique, sécurité)
- Recommandations priorisées (quick wins vs actions structurantes)

## Livrables

- [ ] Interprétation des métriques et goulots d'étranglement
- [ ] Plan de tests complémentaires
- [ ] Proposition de supervision
- [ ] Rapport d'audit : synthèse des constats
- [ ] Rapport d'audit : recommandations priorisées

## Note méthodologique

Cette étape applique une démarche classique d'audit technique : **collecte → analyse des métriques → identification des risques/limites → recommandations formalisées** — la même logique que celle utilisée en audit IT en entreprise (cf. dossier bonus PIKANEX pour un second exemple appliqué).
