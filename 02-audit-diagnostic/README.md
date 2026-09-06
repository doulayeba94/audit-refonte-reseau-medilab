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

a) tests à réaliser

| Problème à investiguer | Outil/Commande | Objectif du test |
|---|---|---|
| Saturation WAN | iperf3 (test de charge dédié hors heures de pointe) + relevé SNMP continu sur 48h | Confirmer le débit réel disponible et identifier précisément les créneaux et la durée des pics de saturation |
| Performance LIMS | Moniteur de ressources serveur (top/htop, Perfmon) + analyse des requêtes lentes côté base de données | Déterminer si la surcharge CPU vient du serveur lui-même (sous-dimensionnement) ou d'une requête applicative mal optimisée |
| Erreurs CRC | Vérification physique du câblage + `show interface` sur le switch (compteurs d'erreurs, statut duplex) | Confirmer si la cause est un défaut de câble, un mauvais connecteur, ou une négociation duplex incorrecte (half/full duplex mismatch) |
| Qualité VoIP | Capture Wireshark sur le flux RTP + mesure de la gigue (jitter) et du délai | Isoler si la dégradation MOS vient de la perte de paquets, de la gigue, ou de la priorisation QoS absente sur le lien WAN |

b) Proposition de solution de supervision à mettre en place

| Indicateur | Équipement | Seuil d'avertissement | Seuil critique |
|---|---|---|---|
| Utilisation bande passante WAN | Routeur/lien Colmar | 75% | 90% |
| Latence (RTT) | Lien WAN inter-sites | 80 ms | 150 ms |
| Taux de perte de paquets | Lien WAN inter-sites | 0,3% | 1% |
| CPU serveur LIMS | Serveur applicatif LIMS | 70% | 90% |
| MOS VoIP | Passerelle/gateway VoIP | 4.0 | 3.5 |

c) Choix logique des seuils d'alertes

Les seuils warning sont fixés avant que le problème devienne visible pour l'utilisateur (permet une action préventive)
Les seuils critical correspondent aux valeurs déjà observées lors de l'audit (ex: 98% de charge WAN, MOS 3.8) — ce qui a été mesuré comme problématique devient le déclencheur d'alerte.


### 3. Ébauche de rapport d'audit
- Synthèse des constats (documentaire, technique, sécurité)

L'audit technique de l'infrastructure réseau MediLab révèle une saturation critique du lien WAN inter-sites (pics à 98% d'utilisation), identifiée comme cause racine de la dégradation généralisée du service : latence élevée (jusqu'à 180ms), perte de paquets (0,5%) et qualité VoIP dégradée (MOS 3.8, sous le seuil acceptable de 4.0).
En parallèle, le serveur applicatif LIMS présente des pics de charge CPU à 95% pendant les heures de pointe (9h-11h), risquant de ralentir l'accès à l'application métier critique des laboratoires. Des erreurs CRC récurrentes (12/jour) sur le port du serveur fichiers signalent un problème matériel non traité, cohérent avec la panne de 6 heures déjà survenue et représentant un risque de récidive. 
Aucune solution de supervision proactive n'étant en place à ce jour, ces dysfonctionnements ne sont détectés qu'après impact sur les
utilisateurs.
L'architecture actuelle, initialement dimensionnée pour un usage plus restreint, ne dispose d'aucune redondance ni de priorisation
du trafic (QoS), ce qui amplifie l'effet de la saturation sur l'ensemble des flux critiques.

- Recommandations priorisées (quick wins vs actions structurantes)

| Recommandation | Priorité | Effort | Bénéfice attendu |
|---|---|---|---|
| Mettre en place une supervision proactive (Zabbix/PRTG) avec les 5 indicateurs définis | Haute | Faible | Détection anticipée des dégradations avant impact utilisateur |
| Remplacer le câble/port défectueux à l'origine des erreurs CRC | Haute | Faible | Élimination du risque de panne récurrente sur le serveur fichiers |
| Mettre en place une politique QoS priorisant VoIP et flux LIMS sur le lien WAN | Haute | Moyen | Amélioration immédiate du MOS VoIP et de la stabilité applicative sans surcoût matériel |
| Augmenter la bande passante du lien WAN ou ajouter un lien de secours (SD-WAN) | Moyenne | Élevé | Résorption durable de la saturation et ajout d'une redondance contre les pannes |
| Dimensionner ou répartir la charge du serveur LIMS (upgrade ou load-balancing) | Moyenne | Élevé | Suppression des pics CPU en heure de pointe et amélioration du temps de réponse SIL |

## Livrables

- [ ] Interprétation des métriques et goulots d'étranglement
- [ ] Plan de tests complémentaires
- [ ] Proposition de supervision
- [ ] Rapport d'audit : synthèse des constats
- [ ] Rapport d'audit : recommandations priorisées

## Note méthodologique

Cette étape applique une démarche classique d'audit technique : **collecte → analyse des métriques → identification des risques/limites → recommandations formalisées** — la même logique que celle utilisée en audit IT en entreprise (cf. dossier bonus PIKANEX pour un second exemple appliqué).
