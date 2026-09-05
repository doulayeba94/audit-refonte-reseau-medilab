# Bonus — Audit et diagnostic d'infrastructure : cas PIKANEX

> Étude de cas indépendante, réalisée en amont du projet MediLab, qui illustre la même démarche d'audit appliquée à un autre secteur d'activité.

## Contexte

**PIKANEX**, société de distribution de pièces mécaniques (~120 collaborateurs, 2 sites : Siège / Stock & Logistique), dispose d'une documentation réseau obsolète (2022) ne reflétant pas les évolutions matérielles et applicatives de 2023.

## Démarche d'audit appliquée

### Étape 1 — Recueil d'informations & remise en question de la documentation existante
- État des lieux documentaire de l'architecture multi-sites (siège, site logistique, interconnexion fibre/MPLS, segmentation VLAN théorique)
- Identification des manques critiques : absence d'inventaire à jour, incertitude sur le pare-feu en production, incohérences VLAN/documentation, plan d'adressage IP incohérent, absence de traçabilité des changements

### Étape 2 — Analyse des besoins et identification des flux critiques
- Cartographie des applications stratégiques (ERP, VoIP, CRM, VPN télétravail, Wi-Fi) avec leur niveau de criticité et leurs exigences (disponibilité, latence)
- Analyse des exigences de SLA (99,9 % de disponibilité, QoS VoIP, montée en charge télétravail) au regard de l'architecture existante (absence de QoS, redondance limitée)
- Mise en évidence des conflits de flux (voix vs applicatif métier vs Internet) faute de hiérarchisation

### Étape 3 — Audit technique de l'architecture existante
- Analyse des performances réseau à partir des logs de supervision (Zabbix) : saturation du lien cœur de réseau, taux d'erreurs CRC, surcharge CPU du pare-feu
- Identification des risques structurels : pare-feu en single point of failure, absence de redondance, segmentation incomplète, retard de patching

## Compétences illustrées

- Remise en cause d'une documentation réseau obsolète et identification des écarts
- Cartographie des flux applicatifs par criticité et exigences de SLA
- Analyse de logs de supervision pour diagnostiquer des goulots d'étranglement
- Identification de risques structurels (SPOF, segmentation, patch management)

## Contenu

Voir le document source détaillé : `Travaux_Pratiques_n_1.docx`
