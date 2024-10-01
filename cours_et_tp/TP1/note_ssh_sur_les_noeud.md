# Configuration et Vérification de SSH sur un Switch Cisco

Cette fiche explique comment configurer un accès SSH sécurisé sur un switch Cisco et comment vérifier cette configuration.

---

## 1. Configuration de SSH sur un switch Cisco

### Étape 1 : Accéder au mode de configuration globale

Accédez au mode de configuration du switch en utilisant les commandes suivantes :
```bash
enable
configure terminal
```

### Étape 2 : Configurer un nom de domaine

Le nom de domaine est nécessaire pour générer les clés cryptographiques :
```bash
ip domain-name example.com
```
Remplacez `example.com` par votre nom de domaine.

### Étape 3 : Générer les clés RSA

Générez une paire de clés RSA (recommandé : 2048 bits pour une meilleure sécurité) :
```bash
crypto key generate rsa modulus 2048
```

### Étape 4 : Créer un utilisateur avec des privilèges

Créez un compte utilisateur local pour l'authentification SSH :
```bash
username admin privilege 15 secret cisco
```
- `admin` : nom d'utilisateur.
- `privilege 15` : droits administratifs complets.
- `cisco` : mot de passe (à remplacer par un mot de passe sécurisé).

### Étape 5 : Configurer les lignes VTY pour permettre SSH uniquement

Restreindre les connexions à SSH et utiliser l'authentification locale :
```bash
line vty 0 4
 transport input ssh
 login local
```

### Étape 6 : Configurer l'interface VLAN de gestion (SVI)

Attribuez une adresse IP à l'interface VLAN utilisée pour la gestion :
```bash
interface vlan 1
 ip address 192.168.1.2 255.255.255.0
 no shutdown
```
- **192.168.1.2** est l'adresse IP de gestion du switch.
- **255.255.255.0** est le masque de sous-réseau.

### Étape 7 : Configurer la passerelle par défaut (si nécessaire)

Si le switch doit être accessible à distance, configurez une passerelle par défaut :
```bash
ip default-gateway 192.168.1.1
```

### Étape 8 : Sauvegarder la configuration

Enregistrez les modifications pour qu'elles persistent après un redémarrage :
```bash
write memory
```

---

## 2. Vérification de la configuration SSH

### Étape 1 : Vérifier l'état du SSH

Assurez-vous que SSH est activé et vérifiez la version :
```bash
show ip ssh
```

### Étape 2 : Vérifier les utilisateurs locaux

Vérifiez que les utilisateurs locaux sont correctement configurés :
```bash
show running-config | include username
```

### Étape 3 : Vérifier les lignes VTY

Assurez-vous que SSH est le seul protocole autorisé et que l'authentification locale est activée :
```bash
show running-config | section line vty
```
Le résultat doit inclure :
```
line vty 0 4
 transport input ssh
 login local
```

### Étape 4 : Vérifier l'interface VLAN de gestion

Assurez-vous que l'interface VLAN de gestion est configurée avec une adresse IP et qu'elle est active :
```bash
show ip interface brief | include Vlan
```
Vous devriez voir une sortie avec l'adresse IP attribuée et le statut **up**.

### Étape 5 : Tester la connexion SSH depuis un autre appareil

Utilisez un client SSH (par exemple, PuTTY, Terminal, ou une commande `ssh`) pour vous connecter au switch depuis une autre machine.

Exemple de commande sous Linux/MacOS ou PowerShell :
```bash
ssh admin@192.168.1.2
```
- Remplacez `admin` par l'utilisateur configuré.
- **192.168.1.2** est l'adresse IP du switch.

### Étape 6 : Vérifier les clés RSA

Pour vérifier que les clés RSA ont bien été générées :
```bash
show crypto key mypubkey rsa
```
Cela affichera la clé publique utilisée pour SSH.

### Étape 7 : Sauvegarder la configuration finale

Après avoir vérifié que tout fonctionne correctement, sauvegardez une dernière fois la configuration :
```bash
write memory
```

---

## 3. Sécurisation avancée (facultatif)

### Limiter les tentatives d'authentification SSH
Pour limiter les tentatives d'authentification échouées et réduire le délai d'attente de connexion :
```bash
ip ssh time-out 60
ip ssh authentication-retries 2
```
Cela impose un délai de 60 secondes et limite les tentatives d'authentification à 2 avant de fermer la session.
