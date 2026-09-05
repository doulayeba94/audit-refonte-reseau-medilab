# Étape 3 — Conception de l'architecture cible

## Contexte

Suite à l'audit, la direction demande une **architecture réseau cible** répondant aux problèmes identifiés et permettant l'expansion vers 3 nouveaux laboratoires dans les 2 prochaines années.

**Contraintes :**
- Budget WAN : 3 000 €/mois maximum pour l'ensemble des sites
- SIL (système d'information de laboratoire) : temps de réponse < 200 ms, disponibilité 99,99 %
- Conformité HDS : segmentation réseau obligatoire pour les données de santé
- 3 nouveaux laboratoires à intégrer (15-25 postes chacun)

## Travail réalisé

### 1. Dimensionnement des liens WAN
Détermination de la bande passante nécessaire par site à partir des données de trafic (siège + labo type), en tenant compte des contraintes budgétaires.

### 2. Plan d'adressage et segmentation VLAN
Proposition d'un plan d'adressage IP et d'une segmentation VLAN conforme aux exigences HDS (séparation biomed / bureautique / voix).

### 3. Schéma d'architecture cible et politique QoS
Définition de la politique QoS garantissant les niveaux de service attendus (SIL, VoIP), et schéma de l'architecture réseau cible :

![Architecture cible](../docs/schemas/architecture-cible.svg)

## Livrables

- [ ] Dimensionnement des liens WAN par site
- [ ] Plan d'adressage IP et VLAN
- [ ] Schéma d'architecture cible
- [ ] Politique QoS
