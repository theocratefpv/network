# Étape 7 : Configurer LACP et PAgP (EtherChannel) sur un switch Cisco

Cette fiche explique comment configurer l'agrégation de liens (EtherChannel) en utilisant **LACP** (Link Aggregation Control Protocol) et **PAgP** (Port Aggregation Protocol), et les concepts associés.

---

## Concepts de base

### 1. EtherChannel

L'EtherChannel permet d'agréger plusieurs interfaces physiques en une seule interface logique. Cela offre :
- **Augmentation de la bande passante** : L'agrégation des ports permet de combiner les capacités des ports physiques.
- **Redondance** : Si un lien physique tombe en panne, les autres continuent de transmettre les données.

### 2. LACP (Link Aggregation Control Protocol)

- **LACP** est un protocole standard défini par **IEEE 802.3ad**.
- Il permet de négocier dynamiquement l'agrégation des ports entre les switches.
- **Modes LACP** :
  - **Active** : Le port initie activement les négociations LACP.
  - **Passive** : Le port attend les requêtes LACP pour commencer l'agrégation.

### 3. PAgP (Port Aggregation Protocol)

- **PAgP** est un protocole propriétaire de Cisco.
- Il permet également de former des agrégats de ports sur les équipements Cisco.
- **Modes PAgP** :
  - **Desirable** : Le port essaie activement de former une agrégation.
  - **Auto** : Le port attend les requêtes PAgP.

### 4. Différences principales entre LACP et PAgP
- **Interopérabilité** : LACP est un protocole ouvert et interopérable avec différents fabricants, tandis que PAgP est propriétaire de Cisco.
- **Compatibilité** : PAgP fonctionne uniquement entre équipements Cisco.

---

## Configuration de LACP

Voici comment configurer une EtherChannel avec **LACP** entre deux switches.

### Étape 1 : Accédez à la configuration des interfaces

```bash
Switch(config)# interface range fastEthernet 0/1 - 2
```

### Étape 2 : Configurez LACP en mode "active"

```bash
Switch(config-if-range)# channel-group 1 mode active
```
- **channel-group 1** : Numéro du groupe EtherChannel.
- **mode active** : Le switch initie activement les négociations LACP.

### Étape 3 : Configurez l'interface logique `Port-channel 1`

```bash
Switch(config)# interface port-channel 1
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan all
```

---

## Configuration de PAgP

Voici comment configurer une EtherChannel avec **PAgP**.

### Étape 1 : Accédez à la configuration des interfaces

```bash
Switch(config)# interface range fastEthernet 0/3 - 4
```

### Étape 2 : Configurez PAgP en mode "desirable"

```bash
Switch(config-if-range)# channel-group 2 mode desirable
```
- **channel-group 2** : Numéro du groupe EtherChannel.
- **mode desirable** : Le port essaie activement de former un agrégat via PAgP.

### Étape 3 : Configurez l'interface logique `Port-channel 2`

```bash
Switch(config)# interface port-channel 2
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan all
```

---

## Vérification de la configuration de l'EtherChannel

### Vérifier l'état de l'EtherChannel

Utilisez la commande suivante pour vérifier l'état de l'EtherChannel :

```bash
show etherchannel summary
```

### Vérifier les détails du `Port-channel`

```bash
show interfaces port-channel 1
```

---

## Chargement équilibré (Load Balancing)

Il est possible de configurer la répartition du trafic à travers les différents liens physiques.

### Configurer le chargement équilibré

```bash
port-channel load-balance src-dst-mac
```
Cette méthode de load balancing répartit le trafic en fonction des adresses MAC source et destination.

---

## Résumé des commandes

### Pour LACP :

```bash
Switch(config)# interface range fastEthernet 0/1 - 2
Switch(config-if-range)# channel-group 1 mode active
Switch(config)# interface port-channel 1
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan all
```

### Pour PAgP :

```bash
Switch(config)# interface range fastEthernet 0/3 - 4
Switch(config-if-range)# channel-group 2 mode desirable
Switch(config)# interface port-channel 2
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan all
```

### Vérifications :

- **État de l'EtherChannel** : 
  ```bash
  show etherchannel summary
  ```

- **Détails du port-channel** :
  ```bash
  show interfaces port-channel 1
  ```

---

## Conclusion

L'EtherChannel avec LACP ou PAgP permet d'augmenter la bande passante et la redondance entre les switches. LACP est recommandé pour les environnements multivendeurs, tandis que PAgP est utile pour les infrastructures entièrement Cisco. Assurez-vous que tous les ports physiques d'un EtherChannel ont des paramètres cohérents (vitesse, duplex, mode de trunk) avant de les agréger.
