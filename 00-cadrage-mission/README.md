# Étape 0 — Cadrage de la mission d'audit

## Contexte

MediLab, groupe de laboratoires d'analyses médicales multi-sites, a connu des problèmes récurrents de lenteur réseau ainsi qu'une panne de 6 heures ayant impacté l'activité. La direction commande une mission d'audit technique complète, suivie d'une refonte de l'architecture réseau.

Avant toute analyse technique, cette étape formalise le cadre de la mission — un livrable généralement produit en tout premier par l'auditeur, en amont de la collecte d'informations.

## Lettre de mission (synthèse)

### Périmètre de l'audit
- **Sites couverts** : le siège (Paris) et les laboratoires régionaux (2 laboratoires existants + 3 nouveaux laboratoires à intégrer dans les 2 ans)
- **Systèmes concernés** : infrastructure réseau (WAN, LAN, VLAN), téléphonie IP, VPN, pare-feu, applicatif métier (LIMS), messagerie
- **Hors périmètre** : audit applicatif détaillé du LIMS lui-même (traité comme boîte noire), audit RH/organisationnel

### Objectifs de la mission
1. Diagnostiquer les causes des dysfonctionnements réseau (lenteurs, panne de 6h)
2. Évaluer la conformité de l'infrastructure aux exigences réglementaires (certification **HDS**)
3. Concevoir une architecture cible capable d'absorber la montée en charge (nouveaux laboratoires, télétravail)
4. Chiffrer et planifier le projet de refonte

### Référentiel(s) utilisé(s)
- **HDS (Hébergeur de Données de Santé)** : exigences réglementaires sectorielles (obligatoires pour un acteur de santé manipulant des données de santé à caractère personnel)
- **EBIOS RM** (ANSSI) : méthode d'analyse de risques, utilisée en version simplifiée pour le volet sécurité
- Bonnes pratiques réseau générales (segmentation, QoS, redondance) en l'absence de norme unique dédiée à l'infrastructure

### Parties prenantes
| Rôle | Implication dans la mission |
|---|---|
| Direction MediLab | Commanditaire, validation des recommandations et du budget |
| Équipe IT interne | Fournit les données techniques (logs, supervision, documentation), interlocuteur technique principal |
| Biologistes / utilisateurs des laboratoires | Expriment les besoins métier (SIL, télétravail, criticité des applications) |
| Auditeur (moi) | Réalise le diagnostic, l'analyse de risques, propose l'architecture cible et le chiffrage |

### Contraintes identifiées dès le cadrage
- Budget : CAPEX max 250 000 €, OPEX max 5 000 €/mois
- Délai : déploiement en 6 mois maximum
- Exigence réglementaire non négociable : conformité HDS
- Continuité de service pendant la migration (activité médicale ne pouvant être interrompue)

## Pourquoi cette étape compte

Un audit qui commence directement par la collecte technique sans cadrage préalable risque de :
- Auditer un périmètre trop large ou mal défini (perte de temps, dilution des priorités)
- Appliquer un référentiel inadapté au contexte métier
- Produire des recommandations qui ignorent les contraintes réelles (budget, délai, parties prenantes)

C'est cette étape qui donne sa légitimité et sa structure à toute la suite de la mission (étapes 1 à 5).
