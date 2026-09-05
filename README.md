# Audit et refonte de l'infrastructure réseau multi-sites — Cas MediLab

> Mission fictive d'audit IT et de conception réalisée dans le cadre de ma formation (Master Cybersécurité & Systèmes d'Information). Étude de cas pédagogique, structurée et rédigée pour refléter une mission d'audit réelle en entreprise.

## Contexte

**MediLab** est un groupe de laboratoires d'analyses médicales multi-sites (un siège + plusieurs laboratoires régionaux). Suite à des problèmes récurrents de lenteur réseau et à une panne de 6 heures, la direction commande un **audit technique complet**, suivi d'une **refonte de l'architecture réseau**, avec une exigence forte de conformité **HDS** (Hébergeur de Données de Santé).

Ce dépôt documente les 5 étapes de la mission, de l'analyse des flux existants jusqu'au chiffrage et à la planification du projet — soit le déroulé complet d'une mission d'audit et de conseil en infrastructure réseau.

## Sommaire de la mission

| Étape | Dossier | Objectif |
|---|---|---|
| 0 | [`00-cadrage-mission`](./00-cadrage-mission) | Cadrer la mission : périmètre, objectifs, référentiel (HDS, EBIOS RM), parties prenantes et contraintes |
| 1 | [`01-caracterisation-flux`](./01-caracterisation-flux) | Identifier et qualifier les flux réseau existants (voix, applicatifs, VPN) |
| 2 | [`02-audit-diagnostic`](./02-audit-diagnostic) | Diagnostiquer l'infrastructure existante à partir des métriques de supervision, identifier les goulots d'étranglement et produire un rapport d'audit |
| 3 | [`03-architecture-cible`](./03-architecture-cible) | Concevoir l'architecture réseau cible (dimensionnement WAN, plan d'adressage/VLAN, politique QoS) |
| 4 | [`04-securite-conformite`](./04-securite-conformite) | Réaliser une analyse de risques (inspirée EBIOS RM), définir les règles de filtrage et vérifier la conformité HDS |
| 5 | [`05-chiffrage-planification`](./05-chiffrage-planification) | Chiffrer le projet (CAPEX/OPEX), calculer le TCO/ROI et établir le planning de déploiement |

**Bonus :** [`bonus-audit-pikanex`](./bonus-audit-pikanex) — un second cas d'audit et de diagnostic d'infrastructure (entreprise PIKANEX, secteur distribution), traité indépendamment, qui illustre la méthodologie d'audit appliquée à un contexte différent.

## Méthodologie appliquée

- **Audit réseau** : analyse de métriques de supervision, identification de goulots d'étranglement, plan de tests complémentaires
- **Analyse des flux** : classification (temps réel / interactif / transactionnel / bulk), matrice de flux, dimensionnement de bande passante (VoIP, WAN)
- **Conception d'architecture** : segmentation VLAN, plan d'adressage IP, politique QoS, choix VPN (IPSec vs SSL)
- **Gestion des risques & conformité** : analyse de risques inspirée du référentiel **EBIOS RM**, règles de filtrage pare-feu par zones de sécurité, audit de conformité **HDS**
- **Gestion de projet** : chiffrage CAPEX/OPEX, calcul de TCO et ROI, planning de déploiement (Gantt)

## Compétences démontrées

- Audit technique et diagnostic d'infrastructures réseau
- Analyse et gestion des risques informatiques (méthode EBIOS RM)
- Connaissance des exigences réglementaires sectorielles (HDS / données de santé)
- Conception d'architectures réseau sécurisées (segmentation, VPN, QoS)
- Chiffrage et pilotage financier de projets IT (CAPEX, OPEX, TCO, ROI)
- Formalisation de constats et de recommandations dans un rapport d'audit

## Stack / outils mobilisés

`VLAN` · `VPN IPSec / SSL` · `QoS` · `Supervision réseau (type Zabbix)` · `EBIOS RM` · `Pare-feu NGFW` · `802.1X / NAC` · `SIEM`

---

*Chaque dossier contient le contexte de l'atelier, les questions traitées et mon analyse/mes livrables (tableaux, schémas, recommandations).*
