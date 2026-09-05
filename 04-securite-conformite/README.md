# Étape 4 — Sécurité et conformité (volet HDS)

## Contexte

MediLab prépare sa **certification HDS** et doit démontrer la conformité de son infrastructure réseau aux exigences réglementaires. Le volet sécurité doit répondre aux vulnérabilités identifiées lors de l'audit :

- Pas de pare-feu NGFW (simple ACL sur routeur)
- Segmentation insuffisante (VLAN unique biomed + bureautique)
- Aucun contrôle d'accès réseau (NAC) ni authentification 802.1X
- Flux SIL non chiffrés entre sites
- Pas de journalisation centralisée ni SIEM

## Travail réalisé

### 1. Analyse de risques simplifiée (inspirée EBIOS RM)
Évaluation de 5 scénarios de menace : probabilité (1-4), gravité (1-4), niveau de risque (probabilité × gravité), et mesures de réduction associées.

### 2. Règles de filtrage pare-feu
Définition des zones de sécurité et table de règles de filtrage pare-feu correspondantes.

### 3. Audit de conformité HDS
Vérification de la conformité de l'architecture cible aux exigences de la certification HDS.

## Livrables

- [ ] Tableau d'analyse de risques (EBIOS RM simplifiée)
- [ ] Zones de sécurité et règles de filtrage pare-feu
- [ ] Grille d'audit de conformité HDS
