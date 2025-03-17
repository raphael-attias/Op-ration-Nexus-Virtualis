# Opération Nexus Virtualis – Documentation en Markdown pour GitHub

Cette documentation décrit en détail l'ensemble des étapes de l'opération Nexus Virtualis. Vous y trouverez les consignes pour comprendre les concepts de base des hyperviseurs, récupérer les images ISO, installer et configurer Hyper‑V, ESXi, ProxMox, mettre à jour Xen et installer Xen‑Orchestra, ainsi que les procédures de migration inter‑hyperviseurs.

---

## Table des Matières
1. [Job 1 – Concepts de Base](#job-1---concepts-de-base)
2. [Job 2 – Récupération des Images ISO](#job-2---récupération-des-images-iso)
3. [Job 3 – Configuration de l’hyperviseur Hyper‑V sur Windows Server 2022](#job-3---configuration-de-lhyperviseur-hyper-v-sur-windows-server-2022)
4. [Job 4 – Installation de l’hyperviseur ESXi](#job-4---installation-de-lhyperviseur-esxi)
5. [Job 5 – Installation de l’hyperviseur ProxMox](#job-5---installation-de-lhyperviseur-proxmox)
6. [Job 6 – Mise à Jour de Xen et Installation de Xen‑Orchestra](#job-6---mise-à-jour-de-xen-et-installation-de-xen‑orchestra)
7. [Job 7 – Migration Inter‑Hyperviseurs](#job-7---migration-inter‑hyperviseurs)
8. [Job 8 – (À Compléter)](#job-8---à-compléter)

---

## Job 1 – Concepts de Base

*Expliquer les concepts de base :*  
- Différence entre hyperviseurs de type 1 et type 2.  
- Avantages et inconvénients des hyperviseurs de type 1.  
- Cas d'utilisation typiques.

### 1. Différence entre hyperviseurs de type 1 et type 2

Un hyperviseur est un logiciel qui permet de créer, gérer et exécuter des machines virtuelles (VM) sur une plateforme physique. Il existe deux principaux types d'hyperviseurs :

#### Hyperviseur de type 1 (ou "bare‑metal")
- **Définition** : S'exécute directement sur le matériel physique, sans nécessiter un système d'exploitation hôte, permettant une gestion native des ressources.
- **Exemples** : VMware ESXi, Microsoft Hyper‑V, ProxMox VE.

#### Hyperviseur de type 2 (ou "hosted")
- **Définition** : Fonctionne sur un système d'exploitation hôte, qui gère lui-même l’accès aux ressources matérielles.
- **Exemples** : VMware Workstation, Oracle VirtualBox, Parallels Desktop.

### 2. Avantages et Inconvénients des Hyperviseurs de Type 1

#### Avantages
- **Performance optimale** : Gestion directe des ressources sans intermédiaire.
- **Sécurité renforcée** : Meilleur isolement des machines virtuelles.
- **Stabilité et efficacité** : Moins sujet aux défaillances dues à l'OS hôte.

#### Inconvénients
- **Complexité d'installation et de gestion** : Installation et configuration souvent plus complexes.
- **Moins de flexibilité pour les environnements de bureau**.
- **Coût** : Solutions professionnelles pouvant nécessiter des licences coûteuses.

### 3. Cas d'Utilisation Typiques
- **Virtualisation des serveurs** dans les centres de données.
- **Cloud computing** pour des environnements public ou privé.
- **Environnements de test et développement** isolés.
- **Haute disponibilité et reprise après sinistre** grâce à la migration dynamique des VMs.

---

## Job 2 – Récupération des Images ISO

Vous devez récupérer les images ISO des hyperviseurs cibles essentielles pour la mission :

- **Hyper‑V**  
  (Dépend de l’installation de Windows Server 2022)

- **VMware ESXi 8** (non officiel)  
  [Lien vers ESXi 8](https://gist.github.com/ayebrian/646775424393c9a35fb8257f44df1c8b#esxi-8)

- **ProxMox VE 8.3** (officiel)  
  [Lien vers ProxMox VE 8.3](https://www.proxmox.com/en/downloads/proxmox-virtual-environment/iso/proxmox-ve-8-3-iso-installer)

- **XCP-ng** (officiel)  
  [Documentation et installation de XCP-ng](https://docs.xcp-ng.org/installation/install-xcp-ng/)

---

## Job 3 – Configuration de l’hyperviseur Hyper‑V sur Windows Server 2022

### Pré-requis
- Si Windows Server est installé sur un hyperviseur (ex. VMware), pensez à activer la virtualisation du processeur.

### Installation de Hyper‑V

1. **Lancement du Gestionnaire de serveur**  
   Ouvrez le Gestionnaire de serveur sur le futur hyperviseur.

2. **Ajout du rôle Hyper‑V**  
   Cliquez sur le bouton *Créer* puis sélectionnez *Ajouter des rôles et fonctionnalités*.

3. **Sélection du rôle Hyper‑V**  
   À l’étape *Rôles de serveur*, cochez la case **Hyper‑V** dans la liste.  
   Validez ensuite l’installation des fonctionnalités complémentaires, notamment :  
   - La console *Gestionnaire Hyper‑V*.  
   - Le module PowerShell pour Hyper‑V (permettant la gestion via des commandes spécifiques).

4. **Finalisation de l’installation**  
   Une fois l’installation terminée, redémarrez le serveur pour appliquer les modifications.

### Création d’une Machine Virtuelle

1. **Accès à Hyper‑V Manager**  
   Dans la barre de recherche de Windows, tapez "Hyper‑V Manager" et lancez l’application.

2. **Création d’un nouvel ordinateur virtuel**  
   Cliquez sur *Nouveau > Ordinateur virtuel*.

3. **Configuration de la VM**  
   - **Nom et emplacement** : Définissez le nom de la machine virtuelle et choisissez l’emplacement de stockage.  
   - **Génération** : Sélectionnez *Génération 2*.  
   - **Mémoire** : Allouez 1 Go de RAM.  
   - **Réseau** : Choisissez la carte réseau du serveur pour garantir l’accès à Internet.  
   - **Installation du système** : Sélectionnez l’ISO approprié (par exemple, une ISO Debian).

4. **Vérifications Préliminaires**  
   Avant de démarrer la VM, vérifiez que toutes les configurations (nom, stockage, génération, mémoire, réseau et ISO) sont correctes.

5. **Démarrage et Vérification**  
   Lancez la VM via Hyper‑V Manager et vérifiez qu’elle est opérationnelle.

---

## Job 4 – Installation de l’hyperviseur ESXi

### Création de la Machine Virtuelle dans VMware

- Créez une machine virtuelle dans VMware en définissant les caractéristiques spécifiques nécessaires pour héberger l’hyperviseur ESXi (nombre de CPU, quantité de mémoire, capacité de stockage, etc.).  
- Assurez-vous que la configuration répond aux prérequis d’ESXi.

### Installation Locale d'ESXi

1. Démarrez la VM et lancez l’installation d’ESXi en mode local.  
2. Sélectionnez la bonne option d’installation et appuyez sur la touche Entrée pour démarrer le processus.  
3. Confirmez l’installation et la répartition du disque selon les indications à l’écran.

### Accès à la Console Web d'ESXi

- Une fois l’installation terminée, l’écran de la VM affiche l’adresse IP de l’hyperviseur.  
- Ouvrez un navigateur web, saisissez cette adresse IP et connectez-vous avec l’utilisateur et le mot de passe configurés pendant l’installation.

### Création d’une Machine Virtuelle sous ESXi

1. Dans la console web, cliquez sur *Créer/Enregistrer une VM* pour lancer l’assistant de création.  
2. Suivez attentivement toutes les étapes de configuration (nom, stockage, ressources matérielles, etc.).  
3. Vérifiez que la VM apparaît correctement dans la liste des machines virtuelles.

---

## Job 5 – Installation de l’hyperviseur ProxMox

### Préparation de l'Environnement

Avant de créer une VM, il est recommandé de mettre en place une pool de stockage pour organiser et isoler les disques.

### Création d'une Pool de Stockage

1. Dans l’onglet *Datacenter* de ProxMox, accédez aux menus.  
2. Cliquez sur *Permissions*, puis sur *Pools*.  
3. Dans l’onglet *Create*, donnez un nom à la pool et validez sa création.  
4. Vérifiez que la pool apparaît bien dans l’interface.

### Ajout de l'ISO

- Ajoutez l’image ISO nécessaire à l’installation du système d’exploitation de la VM.  
- Vérifiez que l’ISO est bien disponible dans la section dédiée aux fichiers ISO.

### Création d’une Machine Virtuelle sous ProxMox

1. Lancez le processus de création d’une VM via l’interface ProxMox.  
2. Suivez toutes les étapes de l’assistant de configuration (choix du système, allocation des ressources, etc.).  
3. Une fois la configuration terminée, vérifiez que la VM est bien créée et opérationnelle.

---

## Job 6 – Mise à Jour de Xen et Installation de Xen‑Orchestra

### Mise à Jour de Xen

1. Exécutez la commande `yum upgrade` pour mettre à jour Xen.  
2. Vérifiez que la mise à jour s'est effectuée correctement sans erreur.

### Installation de Xen‑Orchestra

1. Installez Xen‑Orchestra en utilisant le script intégré fourni avec votre distribution.  
2. Une fois l’installation terminée, lancez Xen‑Orchestra et vérifiez l’interface pour confirmer son bon fonctionnement.

---

## Job 7 – Migration Inter‑Hyperviseurs

Cette section détaille la procédure pour migrer une machine virtuelle entre différents hyperviseurs.

### Migration de Hyper‑V vers ESXi

1. **Préparation sur Hyper‑V**  
   - Installez et ouvrez *vCenter Converter Standalone* sur le Windows Server.  
   - Éteignez la VM Debian (nommée « debian test ») à migrer.

2. **Conversion avec vCenter Converter**  
   - Sélectionnez *Convertir une machine* dans vCenter Converter.  
   - Configurez la connexion vers le serveur Hyper‑V.  
   - Sélectionnez la VM « debian test ».  
   - Entrez les informations de connexion de l’hyperviseur ESXi qui recevra la VM.  
   - Lancez le transfert et vérifiez sur l’hyperviseur ESXi que la VM apparaît correctement.

### Migration d'ESXi vers ProxMox

1. **Exportation depuis ESXi**  
   - Dans vCenter, sélectionnez la VM « debian test ».  
   - Choisissez l’option *Exporter* et exportez la VM en local (en sélectionnant tous les composants).

2. **Préparation sur ProxMox**  
   - Créez une VM dans ProxMox sans disque ni ISO.  
   - Ajustez la taille minimale du disque afin d'importer l'ancien disque.

3. **Importation dans ProxMox**  
   - Déplacez les fichiers de la VM exportée vers le stockage souhaité dans ProxMox.  
   - Renommez le fichier VMDK si nécessaire.  
   - Exécutez la commande d’importation pour intégrer le disque à la VM.  
   - Vérifiez que le disque est correctement monté et que la VM fonctionne.

### Migration d’une VM de ProxMox vers XCP‑ng

1. **Arrêt de la VM dans ProxMox**  
   - Connectez-vous à l’interface Web de ProxMox.  
   - Sélectionnez la VM à migrer et arrêtez-la.

2. **Création d'une Sauvegarde de la VM**  
   - Connectez-vous en SSH sur le serveur ProxMox.  
   - Exécutez la commande suivante pour créer une sauvegarde au format VMA compressé en Zstandard :  
     `vzdump <VMID> --compress zstd`  
     (exemple : `vzdump 8989 --compress zstd`)  
   - Le fichier de sauvegarde sera enregistré dans `/var/lib/vz/dump/`.

3. **Téléchargement et Décompression**  
   - Utilisez un client SFTP/FTP (par exemple, WinSCP) pour télécharger le fichier de sauvegarde sur votre machine locale.  
   - Décompressez le fichier pour obtenir un fichier `.vma`.

4. **Conversion en Format OVF**  
   - Comme XCP‑ng ne supporte pas le format VMA, convertissez-le en OVF.  
   - Utilisez VirtualBox pour importer la VM (si nécessaire, renommez ou convertissez le fichier en `.ova` ou `.vmdk`) puis exportez-la au format OVF (ou OVA).

5. **Importation dans XenCenter (XCP‑ng)**  
   - Ouvrez XenCenter et connectez-vous à votre hôte XCP‑ng (en renseignant IP, Username et Password).  
   - Cliquez sur *File > Import* et sélectionnez le fichier `.ovf` (ou `.ova`).  
   - Suivez l’assistant en configurant l’hôte, le stockage et le réseau.  
   - Finalisez l’importation et vérifiez que la VM apparaît dans XenCenter.

6. **Vérifications Finales**  
   - Démarrez la VM dans XenCenter.  
   - Vérifiez que le système d’exploitation détecte correctement les composants (cartes réseau, disque, etc.).  
   - Installez ou mettez à jour les outils XCP‑ng (ou Citrix VM Tools) pour optimiser la performance.

---

## Job 8 – (À Compléter)

*Cette section est réservée à des instructions supplémentaires ou à des améliorations futures. Veuillez la compléter en fonction de l'évolution du projet.*

---

## Conclusion

Cette documentation couvre l'ensemble des étapes nécessaires pour mener à bien l'opération Nexus Virtualis, depuis la compréhension des concepts de base jusqu'à la migration inter‑hyperviseurs. Chaque section a été rédigée pour guider pas à pas l'installation, la configuration et la migration des machines virtuelles. N'hésitez pas à enrichir cette documentation avec des captures d'écran, des scripts et des commentaires basés sur vos tests et retours d'expérience.

---

## Dépôt du Projet

Le projet complet doit être rendu sur GitHub à l'adresse suivante :  
https://github.com/prenom-nom/NexusVirtualis

---

## Compétences Visées

- Administration système  
- Virtualisation

---

*Bonne exploration dans l'univers de la virtualisation et réussite dans vos missions !*
