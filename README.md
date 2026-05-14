# 🔐 LaboRootSecu - Exploration du Rooting Android

> **Auteur** : Soukaina Bachir  
> **Date** : 15 mai 2026  
> **Contexte** : Laboratoire académique - AVD Pixel 6 Pro (API 33)  
> **Données** : 100% fictives  
> **Objectif** : Comprendre le rooting Android, ses impacts sécurité et les bonnes pratiques de test  

---

## 📋 Table des matières

- [🛠️ Environnement](#-environnement)
- [🎯 Objectifs du lab](#-objectifs-du-lab)
- [📸 Preuves visuelles](#-preuves-visuelles)
- [🧪 Scénarios de test](#-scénarios-de-test)
- [📋 Fiche périmètre](#-fiche-périmètre)
- [🔒 Verified Boot & AVB](#-verified-boot--avb)
- [⚠️ Matrice des risques](#️-matrice-des-risques)
- [🛡️ Mesures défensives](#️-mesures-défensives)
- [📜 OWASP MASVS](#-owasp-masvs)
- [🔬 OWASP MASTG](#-owasp-mastg)
- [🔧 Commandes utilisées](#-commandes-utilisées)
- [🧹 Checklist de fin de session](#-checklist-de-fin-de-session)
- [💡 Réflexion personnelle](#-réflexion-personnelle)

---

## 🛠️ Environnement

| Élément | Valeur |
|---------|--------|
| **Support** | AVD Pixel 6 Pro |
| **Version Android** | 13 (API 33) |
| **Application** | LaboRootSecu v1.0 |
| **Package** | `com.example.laborootsecu` |
| **Langage** | Java |
| **Réseau** | Isolé (mode avion) |
| **Données** | Fictives (ChaiseBleue, TableVerte, LampeLune) |
| **OS Hôte** | Windows 10 |

---

## 🎯 Objectifs du lab

1.  **Comprendre ce que le root change** dans le système Android
2.  **Observer l'impact** sur les mécanismes de sécurité (sandboxing, permissions)
3.  **Accéder aux données privées** d'une application normalement protégées
4.  **Documenter les risques** et les mesures défensives associées
5.  **Appliquer les standards OWASP** (MASVS + MASTG)

---

## 📸 Preuves visuelles

### 1. Appareil connecté
<img width="219" height="71" alt="01_device_ready" src="https://github.com/user-attachments/assets/0dd0aaa0-912b-4269-8c39-d86949327ea5" />

> *L'émulateur Pixel 6 Pro est détecté par ADB. État : `device`.*

---

### 2. Root activé
<img width="497" height="89" alt="02_root_confirmed" src="https://github.com/user-attachments/assets/a6188b67-b2f6-43fb-ba9c-95dae64b193e" />

> *`adb root` redémarre le daemon en mode root. `adb shell id` confirme `uid=0(root) gid=0(root)`.*

---

### 3. Shell root
<img width="244" height="80" alt="03_root_shell" src="https://github.com/user-attachments/assets/ae43cced-c567-4796-b8c2-d699e586e87f" />

> *Le prompt `#` dans le shell Android indique les privilèges super-utilisateur. Aucune commande `su` nécessaire.*

---

### 4. Application en exécution


https://github.com/user-attachments/assets/77b7ba8b-7a4f-453f-a3ba-3a3cbc03d54e


> *L'application LaboRootSecu affiche le résultat de la recherche "ChaiseBleue", un article fictif avec prix et stock.*

---

### 5. Accès aux données privées
<img width="364" height="38" alt="05_data_access" src="https://github.com/user-attachments/assets/f999a822-1056-483d-bee6-f0df55c41eec" />

> *Grâce au root, le dossier `/data/data/com.example.laborootsecu/` est accessible. Les dossiers `cache` et `code_cache` sont visibles, preuve que le sandboxing est contourné.*

---

## 🧪 Scénarios de test

| # | Scénario | Action utilisateur | Résultat attendu | Résultat observé |
|---|----------|-------------------|------------------|------------------|
| 1 | **Écran d'accueil** | Ouvrir l'application | Interface avec titre et barre de recherche | ✅ Conforme |
| 2 | **Recherche** | Saisir "ChaiseBleue" → Rechercher | Article trouvé avec détails | ✅ Conforme |
| 3 | **Fiche détail** | Consulter les informations | Prix, stock, catégorie affichés | ✅ Conforme |

---

## 📋 Fiche périmètre

| Champ | Valeur |
|-------|--------|
| **Application** | LaboRootSecu v1.0 |
| **Support** | AVD Pixel 6 Pro (émulateur) |
| **Objectif** | Comprendre le rooting et ses impacts |
| **Données** | 100% fictives (articles inventés) |
| **Réseau** | Test isolé, mode avion |
| **Comptes** | Aucun compte personnel utilisé |

---

## 🔒 Verified Boot & AVB

### 🎯 Objectif principal
Garantir que le système d'exploitation qui démarre est **exactement celui prévu par le fabricant**, sans aucune modification malveillante.

### ⛓️ Chaîne de confiance (Chain of Trust)
Série de vérifications en cascade où **chaque composant valide l'authenticité du suivant** avant de lui transférer le contrôle. Si un seul maillon est corrompu, toute la chaîne s'effondre.
ROM ➜ Bootloader ➜ Vérification signature ➜ Kernel ➜ Système Android ➜ Applications

text

### ⚠️ Pourquoi l'intégrité au démarrage est critique
Si le démarrage est compromis, **toutes les protections ultérieures peuvent être contournées** : sandboxing, permissions, chiffrement. C'est la porte d'entrée de la forteresse.

### 🔄 AVB (Android Verified Boot)
- Version 2.0 du mécanisme de vérification
- Ajoute une **protection anti-rollback** qui empêche d'installer d'anciennes versions vulnérables
- Utilise **vbmeta** pour vérifier l'intégrité des partitions

---

## ⚠️ Matrice des risques

| # | Risque | Impact potentiel |
|---|-------|------------------|
| **R1** | Intégrité système non garantie | Conclusions biaisées sur la sécurité réelle de l'application |
| **R2** | Surface d'attaque accrue | Exposition à des menaces si l'appareil sort du périmètre de test |
| **R3** | Données sensibles exposées | Violation de confidentialité si données réelles présentes |
| **R4** | Instabilité du système | Tests non reproductibles, résultats incohérents |
| **R5** | Mélange comptes personnels et test | Fuite possible d'informations privées |
| **R6** | Nettoyage insuffisant | Persistance de données résiduelles après les tests |
| **R7** | Réseau non isolé | Effets involontaires sur des systèmes externes |
| **R8** | Traçabilité insuffisante | Impossibilité de reproduire ou d'auditer les tests |

---

## 🛡️ Mesures défensives

| # | Mesure | Objectif |
|---|--------|----------|
| **M1** | Réseau isolé (mode avion) | Empêcher toute communication non contrôlée |
| **M2** | Données 100% fictives | Éliminer tout risque de fuite réelle |
| **M3** | AVD dédié aux tests | Isoler l'environnement de test du quotidien |
| **M4** | Snapshots ou wipe en fin de session | Ne laisser aucune trace persistante |
| **M5** | Journal de configuration détaillé | Garantir la reproductibilité des tests |
| **M6** | Aucun compte personnel | Éviter tout mélange de données |
| **M7** | Contrôle strict des APK installées | Limiter les risques d'installation malveillante |
| **M8** | Captures horodatées + documentation | Assurer une traçabilité complète |

---

## 📜 OWASP MASVS

### Exigence STORAGE-1
> *"Les données sensibles doivent être stockées de manière sécurisée en utilisant des fonctions de chiffrement appropriées."*

Les clés API, mots de passe et tokens ne doivent **jamais** être stockés en clair dans les SharedPreferences ou les bases de données SQLite.

### Exigence NETWORK-1
> *"Les communications réseau doivent utiliser TLS avec une configuration correcte et vérifier les certificats."*

Tout le trafic réseau doit être chiffré avec TLS 1.2+ et l'application doit valider les certificats pour empêcher les attaques Man-in-the-Middle.

---

## 🔬 OWASP MASTG

### Test 1 : Inspection des SharedPreferences
En environnement rooté, examiner le contenu de `/data/data/[package]/shared_prefs/` pour détecter la présence de données sensibles en clair.

**Résultat observé** : Le dossier `shared_prefs` n'existait pas encore car l'application n'avait pas sauvegardé de préférences. L'accès au répertoire parent a néanmoins prouvé le contournement du sandboxing.

### Test 2 : Analyse des logs applicatifs
Utiliser `adb logcat` pour détecter des fuites d'informations sensibles pendant l'exécution de l'application.

**Piste d'amélioration** : Implémenter un système de logs qui capture des informations pour vérifier qu'aucune donnée sensible n'est journalisée.

---

## 🔧 Commandes utilisées

| Commande | Utilité |
|----------|---------|
| `adb devices` | Vérifier la connexion de l'émulateur |
| `adb root` | Activer le mode root sur le daemon ADB |
| `adb shell id` | Confirmer les privilèges root (uid=0) |
| `adb shell` | Ouvrir un shell interactif sur l'appareil |
| `adb shell getprop ro.boot.verifiedbootstate` | Vérifier l'état de l'intégrité au démarrage |
| `adb shell getprop ro.boot.veritymode` | Vérifier le mode verity |
| `ls /data/data/com.example.laborootsecu/` | Accéder au répertoire privé de l'application |
| `adb install app-debug.apk` | Installer l'application de test |
