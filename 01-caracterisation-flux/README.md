# Étape 1 — Caractérisation des flux et analyse réseau

## Contexte

Données trafic fournies par l'équipe IT de MediLab :
- Heures de pointe : 9h-11h et 14h-16h (80 % du trafic)
- Volume quotidien : 50 Go échangés entre laboratoires et siège
- Téléphonie : 60 postes VoIP, codec G.711 (87 kbps/appel)
- Liaisons WAN : Fibre 100 Mbps par site, VPN IPSec.

## Travail réalisé

### 1. Classification des flux
Classification des flux MediLab (téléphonie IP, application LIMS, messagerie Exchange, partage de fichiers, sauvegardes, navigation web) selon leur type (temps réel / interactif / transactionnel / bulk), leur protocole/port et leur priorité QoS.

| Flux | Type | Proto/Port | Priorité QoS |
|---|---|---|---|
| Téléphonie IP | Temps réel | UDP/RTP | Haute |
| Application LIMS | Transactionnel | TCP/443 | Moyenne |
| Sauvegardes | Bulk | TCP/22 | Basse |		



### 2. Calcul de bande passante VoIP
Calcul du nombre d'appels simultanés en heure de pointe, de la bande passante VoIP nécessaire, et vérification de la suffisance de la liaison 100 Mbps.
A partir des données trafic fournies par l'équipe IT la bande passante VoIP nécessaire est calculée ci-dessous:

| Éléments | Valeur / calcul | Résultat |
|---|---|---|
| Appels simultanés maximum | 60 × 87 kbps | 5 220 kbps |
| Bande passante VoIP nécessaire | 5 220 / 1 000 | 5,22 Mbps |
| Marge de sécurité | 5,22 × 30 % | 1,57 Mbps |
| Bande passante avec la marge | 5,22 + 1,57 | 6,79 Mbps |
| Bande passante WAN disponible | — | 100 Mbps/site |
| Bande passante restante | 100 − 6,79 | 93,21 Mbps |
| Conclusion | 6,79 < 100 | Liaison suffisante |

> **NB :** La marge de 30 % prend en compte les en-têtes IP/UDP/RTP, le VPN IPSec et une marge d'exploitation..


### 3. Matrice de flux
Construction de la matrice de flux (source, destination, protocole/port, description, criticité) pour les échanges clés (postes labo ↔ Srv-LIMS, téléphonie IP ↔ IPBX, Srv-LIMS ↔ Sécurité Sociale, etc.).

| source | destination | protocole/port | description | criticité |
|---|---|---|---|---|
| poste labo ↔ Srv-LIMS | Srv LIMS             | TCP/443             |  accés application LIMS  | critique |
| téléphonie IP ↔ IPBX | Srv-IPBX | UDP/5060 | Signalisation SIP | haute |
| Srv-LIMS ↔ Sécurité Sociale | Sécurité Sociale | https/tcp 443 | échange de données sécurisées | critique |

NB: Nous rappelons que cette matrice qui décrit les communications entre les différentes zones ou équipements du réseau est essentielle pour la conception des règles de filtrage et la compréhension des dépendances.

### 4. Plan VLAN
Proposition d'un plan VLAN pour un laboratoire type (nom, plage IP, équipements rattachés).

| nom | plage IP | équipements rattachés |
| --- |--- | --- |
| Vlan_user | 192.168.10.1 - 192.168.10.254/24 | postes utilisateur imprimantes |
| Vlan_biomed | 192.168.20.1 - 192.168.20.254/24 | automate biomet |
| Vlan_voix | 192.168.30.1 - 192.168.20.254/24 | telephone ip |
| Vlan_Wifi | 192.168.40.1 - 192.168.40.254/24 | wifi |
| Vlan_management | 192.168.99.1 - 192.168.99.254/24 | routeur, switchs |

### 5. Analyse VPN
Analyse de l'architecture VPN IPSec actuelle (avantages/inconvénients), proposition de solution pour le télétravail des biologistes, et comparatif IPSec vs SSL VPN selon les usages (liaison labo-siège, télétravail, accès partenaires).

| Usage | Technologie | Justification |
|---|---|---|
| Connexion laboratoire-siège | VPN IPSec site-à-site | Liaison permanente inter-sites, chiffrement du trafic institutionnel |
| Télétravail biologistes | VPN SSL (client-to-site) | Accès nomade individuel, authentification utilisateur, MFA |
| Accès partenaires externes | VPN IPSec restreint vers DMZ | Flux applicatif défini, isolation stricte du LAN interne, moindre privilège |

Avantage et inconvénients:
a) 3 avantages de l'architecture VPN IPSec actuelle.

1) Sécurité au niveau réseau : chiffrement de bout en bout du trafic entre sites, protégeant les données de santé qui transitent sur les liaisons inter-sites (exigence implicite HDS).
2) Coût maîtrisé : le VPN IPSec s'appuie sur une liaison fibre standard (100 Mbps) sans nécessiter un lien opérateur dédié coûteux comme le MPLS.
3) Simplicité de mise en œuvre : technologie mature, largement supportée par la quasi-totalité des équipementiers réseau (routeurs, pare-feu), facile à déployer sur des sites déjà équipés.

b) 3 inconvénients ou risques

1) Pas de garantie de qualité de service (QoS) : contrairement au MPLS, l'IPSec classique sur Internet/fibre ne garantit ni la latence ni la bande passante — problématique pour les flux critiques (VoIP, SIL) en heure de pointe
2) Point unique de défaillance (SPOF) : si le tunnel VPN ou la liaison fibre tombe, le site est totalement isolé, sans lien de secours (pas de redondance identifiée dans l'architecture actuelle).
3) Overhead de chiffrement : l'encapsulation IPSec réduit le débit utile réel (typiquement 15-25% de perte selon le mode), ce qui réduit d'autant la bande passante réellement disponible sur les 100 Mbps annoncés

### 6. Schéma d'architecture (bonus)
Schéma simplifié de l'architecture réseau MediLab existante (siège, 2 laboratoires, VPN, serveurs principaux, flux critiques) :


![Architecture existante](../docs/schemas/architecture-existante.svg)

## Livrables

- [ ] Tableau de classification des flux
- [ ] Calcul de bande passante VoIP
- [ ] Matrice de flux complète
- [ ] Plan VLAN laboratoire
- [ ] Recommandation technologie VPN par usage
- [ ] Schéma d'architecture existant
