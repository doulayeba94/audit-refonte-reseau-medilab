# Étape 4 — Sécurité et conformité (volet HDS)

## Contexte

MediLab prépare sa **certification HDS** et doit démontrer la conformité de son infrastructure réseau aux exigences réglementaires. Le volet sécurité doit répondre aux vulnérabilités identifiées lors de l'audit :

- Pas de pare-feu NGFW (simple ACL sur routeur)
- Segmentation insuffisante (VLAN unique biomed + bureautique)
- Aucun contrôle d'accès réseau (NAC) ni authentification 802.1X
- Flux SIL non chiffrés entre sites
- Pas de journalisation centralisée ni SIEM

---

## 1. Analyse de risques simplifiée (inspirée EBIOS RM)

### 1.1 Scénarios de menace

| Scénario de menace | Proba. | Gravité | Risque | Mesures de réduction |
|---|---|---|---|---|
| Ransomware ciblant le SIL via phishing | 4 | 4 | **16** | Sensibilisation utilisateurs, filtrage anti-phishing, EDR sur postes, sauvegardes hors-ligne testées |
| Accès non autorisé aux automates biomédicaux depuis le réseau bureautique | 3 | 4 | **12** | Segmentation VLAN stricte (BIOMED isolé), règles de filtrage inter-VLAN, NAC/802.1X |
| Connexion d'un équipement personnel non sécurisé (BYOD) sur le réseau biomédical | 3 | 3 | **9** | NAC/802.1X avec VLAN de quarantaine, politique BYOD formalisée, portail captif |
| Interception des données patients sur le lien WAN inter-sites | 2 | 4 | **8** | Chiffrement VPN IPSec renforcé (IKEv2), contrôle d'intégrité des tunnels |
| Déni de service (DDoS) sur la connexion Internet/SD-WAN | 2 | 3 | **6** | Protection anti-DDoS opérateur, lien SD-WAN redondant, rate limiting |

### 1.2 Classement et stratégie de traitement

**Classement par criticité décroissante :** Ransomware (16) > Accès non autorisé automates (12) > BYOD (9) > Interception WAN (8) > DDoS (6)

| Scénario | Stratégie retenue | Justification |
|---|---|---|
| Ransomware SIL | **Réduire** | Risque le plus critique : combinaison de mesures techniques (EDR, filtrage) et organisationnelles (sensibilisation) pour abaisser probabilité et gravité |
| Accès non autorisé automates | **Réduire** | La segmentation stricte est une exigence HDS non négociable — investissement prioritaire |
| BYOD | **Réduire** | Le NAC/802.1X traite ce risque à la source, à un coût maîtrisé |
| Interception WAN | **Réduire** (risque déjà partiellement couvert) | Le VPN IPSec existant couvre déjà une partie du risque ; le renforcement (IKEv2) suffit |
| DDoS | **Transférer + réduire** | Le volet "transférer" (protection anti-DDoS opérateur) est plus rentable qu'un investissement interne complet, complété par la redondance SD-WAN |

---

## 2. Règles de filtrage pare-feu

### 2.1 Rappel des zones de sécurité

| Zone | Réseau | Contenu |
|---|---|---|
| **BIOMED** | VLAN 10 — 10.1.10.0/25 | Automates, analyseurs biomédicaux |
| **USERS** | VLAN 11 — 10.1.11.0/25 | Postes bureautiques, imprimantes |
| **VOIP** | VLAN 12 — 10.1.12.0/26 | Téléphones IP, visioconférence |
| **SERVERS** | VLAN 14 — 10.1.14.0/26 | SIL (10.1.14.10), ERP (10.1.14.20), Mail (10.1.14.30) |
| **MGMT** | VLAN 999 — 10.1.255.0/26 | Administration réseau |
| **INTERNET** | DMZ + WAN | Accès Internet via proxy (10.1.14.50) |

### 2.2 Table de règles de filtrage (moindre privilège + déni implicite)

| N° | Source | Destination | Port/Proto | Action | Log | Sens | Justification |
|---|---|---|---|---|---|---|---|
| 1 | BIOMED | SERVERS (10.1.14.10) | TCP/443 | ALLOW | Oui | Interne | Les automates transmettent leurs résultats au SIL |
| 2 | USERS | SERVERS (10.1.14.10) | TCP/443 | ALLOW | Oui | Interne | Accès applicatif SIL depuis les postes bureautiques |
| 3 | MGMT | ANY | TCP/22, 443 | ALLOW | Oui | Interne | Administration des équipements réseau |
| 4 | USERS | INTERNET (proxy 10.1.14.50) | TCP/80, 443 | ALLOW | Oui | Sortant | Navigation web filtrée via proxy |
| 5 | BIOMED | USERS | ANY | **DENY** | Oui | Interne | Interdiction stricte biomed → bureautique (exigence HDS) |
| 6 | USERS | BIOMED | ANY | **DENY** | Oui | Interne | Interdiction stricte bureautique → biomed (exigence HDS) |
| 7 | BIOMED | INTERNET | ANY | **DENY** | Oui | Sortant | Aucun accès Internet direct pour les automates (surface d'attaque) |
| 8 | SERVERS (Mail 10.1.14.30) | INTERNET | TCP/25, 587 | ALLOW | Oui | Sortant | Envoi de messagerie sortante |
| 9 | INTERNET (IP partenaire whitelistée) | SERVERS (10.1.14.10) | TCP/443 | ALLOW | Oui | Entrant | Télétransmission de résultats avec la Sécurité Sociale (flux VPN restreint) |
| 10 | ANY | ANY | ANY | **DENY (implicite)** | Oui | Tous | Déni implicite : tout ce qui n'est pas explicitement autorisé est interdit |

---

## 3. Audit de conformité HDS

| Exigence HDS | Statut | Éléments de preuve | Actions correctives |
|---|---|---|---|
| Cloisonnement des environnements santé | Partiellement satisfait | VLAN BIOMED isolé (règles 5-7) | Finaliser les tests d'étanchéité inter-VLAN et auditer après déploiement |
| Chiffrement des données en transit (WAN) | Satisfait | VPN IPSec sur liaisons inter-sites | Migrer vers IKEv2 pour renforcer le chiffrement |
| Chiffrement des données au repos (serveurs SIL) | **Non satisfait** | Aucun chiffrement disque identifié | Déployer un chiffrement au repos (type LUKS/BitLocker) sur les serveurs SIL |
| Traçabilité des accès aux données de santé | **Non satisfait** | Aucune solution de journalisation centralisée | Déployer un SIEM et définir une politique de rétention des logs (1 an minimum) |
| Contrôle d'accès authentifié aux ressources réseau | Partiellement satisfait | NGFW prévu, mais NAC/802.1X non déployé | Déployer le NAC/802.1X sur l'ensemble des ports d'accès |
| Plan de reprise d'activité (PRA) réseau documenté | **Non satisfait** | Aucun PRA identifié | Rédiger et tester un PRA réseau (RTO/RPO définis) |
| Gestion des incidents de sécurité | **Non satisfait** | Aucune procédure formalisée | Rédiger une procédure de gestion des incidents et désigner un référent sécurité |
| Protection contre les codes malveillants (IDS/IPS, antivirus) | **Non satisfait** | Aucun IDS/IPS identifié | Déployer un IDS/IPS sur le NGFW et un antivirus centralisé sur les postes/serveurs |

### Point le plus critique pour la certification HDS

**L'absence de journalisation centralisée (SIEM)** constitue le point le plus critique : la traçabilité des accès aux données de santé est une exigence fondamentale et systématiquement vérifiée en priorité lors des audits de certification HDS — sans preuve d'accès traçable, aucune autre mesure de sécurité ne peut être auditée efficacement.

**Plan d'action prioritaire (3 actions) :**
1. Déployer une solution SIEM centralisée collectant les logs du NGFW, des VLAN critiques et des serveurs SIL
2. Définir une politique de rétention des logs conforme (1 an minimum)
3. Former un référent sécurité à l'exploitation des alertes et à la remontée d'incidents

---

## Livrables

- [x] Tableau d'analyse de risques (EBIOS RM simplifiée)
- [x] Zones de sécurité et règles de filtrage pare-feu
- [x] Grille d'audit de conformité HDS
