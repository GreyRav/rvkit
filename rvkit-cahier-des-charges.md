# rvkit — Cahier des charges v1

> _"Bare metal Zig, without the bare metal pain."_

---

## 1. Problème à résoudre

Le développement bare metal en Zig sur architecture RISC-V implique une configuration manuelle fastidieuse : adressage mémoire, registres périphériques, linker scripts, toolchain — chaque board nécessite une mise en place spécifique et chronophage. Il n'existe pas d'équivalent à Arduino ou PlatformIO pour l'écosystème Zig/RISC-V.

**rvkit résout ce problème** en fournissant des templates préconfigurés par board, permettant au développeur de démarrer immédiatement sans se battre contre l'outillage.

---

## 2. Utilisateurs cibles

rvkit cible principalement les **makers, étudiants et startups hardware** qui cherchent à développer sur des boards RISC-V abordables sans la complexité d'une toolchain fragmentée.

L'outil est conçu pour être accessible aux débutants tout en produisant un code professionnel et sécurisé — répondant ainsi aux besoins des petites structures qui ne peuvent pas se permettre des boards à 20€+ l'unité ni une infrastructure de développement lourde.

Le marché professionnel embarqué traditionnel (C/C++) n'est pas la cible prioritaire de la v1, bien que rvkit puisse séduire des profils curieux de Zig cherchant une alternative moderne au C.

> **En résumé :** un outil pensé _simple à installer, zéro dépendance inutile, prêt à flasher en 2 minutes._

---

## 3. Fonctionnalités v1

Priorisation **MoSCoW** :

|Priorité|Commande|Description|
|---|---|---|
|**Must**|`rvkit new --board <target> <nom>`|Génère un projet Zig préconfiguré (linker script, adresses registres, structure de fichiers)|
|**Must**|`rvkit build`|Compile le projet avec la toolchain Zig adaptée à la board cible|
|**Must**|`rvkit flash`|Flashe le firmware compilé sur la board (via wlink ou esptool selon la cible)|
|**Must**|`rvkit monitor`|Moniteur série intégré dans le TUI pour visualiser les logs|
|**Should**|`rvkit boards`|Liste les boards supportées disponibles|
|**Should**|Config via `rvkit.toml`|Un seul fichier de configuration minimal à la racine du projet|
|**Could**|`rvkit update`|Mise à jour des templates depuis le repo officiel|
|**Could**|TUI interactif|Interface TUI complète pour toutes les commandes ci-dessus|

### Expérience utilisateur cible

```bash
rvkit new --board ch32v003 my_project
cd my_project
# ... tu codes ...
rvkit flash
rvkit monitor
```

---

## 4. Boards supportées

### v1

|Board|Architecture|Notes|
|---|---|---|
|**CH32V003**|RISC-V 32bit|Ultra low-cost (~0.10€ en prod), cible principale maker|
|**ESP32-C3**|RISC-V 32bit|WiFi/BLE intégré, très populaire dans la communauté|

### Architecture extensible

Chaque board est un **profil indépendant** — il est possible d'en ajouter sans modifier le core de l'outil.

### Roadmap boards (post-v1)

- Boards custom **Open & Hack** (business model open core — profils open source, support étendu/hardware payant)
- Autres boards RISC-V maker populaires selon les retours communautaires

---

## 5. Contraintes techniques

### OS supportés

|OS|Support|
|---|---|
|**Linux**|Cible principale, priorité de développement|
|**macOS**|Support best-effort, pas de CI dédiée en v1|
|**Windows**|Support best-effort, pas de CI dédiée en v1|

### Langage

- **Rust stable uniquement** — pas de nightly
- Facilite la maintenance et la contribution communautaire
- Réduit les risques de breaking changes

### Dépendances externes

|Dépendance|Rôle|Type|
|---|---|---|
|`wlink`|Flash via WCH-LinkE pour CH32V003|Obligatoire pour CH32V003, `cargo install wlink`|
|`esptool`|Flash ESP32-C3|Optionnelle, activée selon la board cible|
|Toolchain Zig|Compilation des projets générés|Gérée par l'utilisateur, rvkit vérifie sa présence au démarrage|

### Philosophie

> Zéro dépendance surprise — rvkit liste clairement ce dont il a besoin et guide l'utilisateur si quelque chose manque.

---

## 6. Hors scope v1

### Ce que rvkit n'est PAS

- ❌ **Pas un IDE** — l'utilisateur garde son éditeur (Zed, Neovim, VS Code), rvkit ne remplace pas ZLS
- ❌ **Pas un gestionnaire de packages Zig** — c'est le rôle de `zig build` et du package manager Zig natif
- ❌ **Pas un simulateur/émulateur RISC-V**
- ❌ **Pas un outil de debug avancé** — GDB et OpenOCD restent des outils séparés en v1

### Boards volontairement exclues de la v1

- Toute board non RISC-V
- Les boards RISC-V exotiques ou peu répandues dans la communauté maker

### Philosophie générale

> rvkit fait **peu de choses mais les fait bien** — `new`, `build`, `flash`, `monitor`. C'est tout.

---

## Récapitulatif stack technique

|Couche|Technologie|
|---|---|
|TUI|Rust + `ratatui`|
|Core / Commands|Rust stable|
|Flash CH32V003|`wlink` (Rust)|
|Flash ESP32-C3|`esptool` (Python)|
|Config|TOML (`rvkit.toml`)|
|Stockage profils boards|SQLite (`rusqlite`)|
|Templates boards|Zig|

---

_Document créé le 18/02/2026 — v1.0_
