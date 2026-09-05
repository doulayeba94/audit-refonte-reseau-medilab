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

### 4. Plan VLAN
Proposition d'un plan VLAN pour un laboratoire type (nom, plage IP, équipements rattachés).

### 5. Analyse VPN
Analyse de l'architecture VPN IPSec actuelle (avantages/inconvénients), proposition de solution pour le télétravail des biologistes, et comparatif IPSec vs SSL VPN selon les usages (liaison labo-siège, télétravail, accès partenaires).

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
