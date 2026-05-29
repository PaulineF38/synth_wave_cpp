# SynthWave — Synthétiseur Temps Réel en C++

Synthétiseur polyphonique interactif développé avec [OpenFrameworks](https://openframeworks.cc/). Jouez des notes via votre clavier d'ordinateur, choisissez votre forme d'onde, activez un filtre audio et visualisez le spectre sonore en temps réel.

---
## Contexte du projet

Ce projet a été réalisé en **2025–2026** dans le cadre d'un cours de programmation au **Campus Numérique**.  
C'était la **première expérience en C++** pour chacun des trois membres de l'équipe.

L'objectif pédagogique était de découvrir les bases du C++ à travers un projet concret : la synthèse sonore en temps réel. Nous avons abordé les notions de classes, d'héritage, de gestion de la mémoire, et d'intégration d'une bibliothèque externe (OpenFrameworks + ofxGui).

---

## Quick Start

> **Prérequis :** `git`, `wget`, `make`, `g++` et `sudo` disponibles.
> Testé sur Ubuntu 22.04 / Debian 12.

### Linux — copier-coller dans un terminal, ligne par ligne

```bash
# 1. Télécharger OpenFrameworks
wget https://github.com/openframeworks/openFrameworks/releases/download/0.12.0/of_v0.12.0_linux64gcc6_release.tar.gz

# 2. Extraire l'archive
tar -xzf of_v0.12.0_linux64gcc6_release.tar.gz

# 3. Renommer le dossier pour la suite
mv of_v0.12.0_linux64gcc6_release openFrameworks

# 4. Installer les dépendances système
cd openFrameworks/scripts/linux && sudo ./install_dependencies.sh && cd ../../..

# 5. Compiler les libs OpenFrameworks (une seule fois, ~5 min)
make -C openFrameworks/libs/openFrameworksCompiled/project

# 6. Créer un nouveau projet avec le générateur OF
cd openFrameworks/apps/myApps
../../tools/projectGenerator/projectGenerator -o../../.. -a ofxGui synthwave

# 7. Remplacer les sources générées par celles du projet
cd synthwave
rm -rf src
git clone https://github.com/PaulineF38/synth_wave_cpp.git src

# 8. S'assurer que ofxGui est bien dans addons.make
echo "ofxGui" > addons.make

# 9. Compiler
make

# 10. Lancer l'application
make run
```

---

### Windows — PowerShell (copier-coller dans PowerShell)

> **Prérequis :** [Visual Studio 2022](https://visualstudio.microsoft.com/) avec le workload **"Développement Desktop en C++"** installé.

```powershell
# 1. Télécharger OpenFrameworks pour Windows
Invoke-WebRequest -Uri "https://github.com/openframeworks/openFrameworks/releases/download/0.12.0/of_v0.12.0_vs_release.zip" -OutFile "of.zip"

# 2. Extraire l'archive
Expand-Archive -Path "of.zip" -DestinationPath "."

# 3. Renommer le dossier
Rename-Item "of_v0.12.0_vs_release" "openFrameworks"

# 4. Lancer le Project Generator (interface graphique)
Start-Process "openFrameworks\tools\projectGenerator\projectGenerator.exe"
```

Dans le Project Generator :
1. Choisir `openFrameworks\apps\myApps` comme dossier de destination
2. Nommer le projet `synthwave`
3. Ajouter l'addon `ofxGui`
4. Cliquer sur **Generate**

```powershell
# 5. Cloner les sources dans le projet généré
cd openFrameworks\apps\myApps\synthwave
Remove-Item -Recurse -Force src
git clone https://github.com/PaulineF38/synth_wave_cpp.git src
```

6. Ouvrir `synthwave.sln` dans Visual Studio 2022
7. Passer en mode **Release x64**
8. `Ctrl+F5` pour compiler et lancer

---

## Comment jouer

Une fenêtre 1024×768 s'ouvre avec un piano interactif et un panneau de contrôle à droite.

### Mapping clavier → notes

| Touche | Note  | | Touche | Note   |
|--------|-------|-|--------|--------|
| `Q`    | Do 3  | | `Z`    | Do# 3  |
| `S`    | Ré 3  | | `E`    | Ré# 3  |
| `D`    | Mi 3  | | `T`    | Fa# 3  |
| `F`    | Fa 3  | | `Y`    | Sol# 3 |
| `G`    | Sol 3 | | `U`    | La# 3  |
| `H`    | La 3  | | `O`    | Do# 4  |
| `J`    | Si 3  | | `P`    | Ré# 4  |
| `K`    | Do 4  |
| `L`    | Ré 4  |
| `M`    | Mi 4  |

Les touches blanches sont sur la rangée du bas, les touches noires (dièses) sont en haut — comme un vrai clavier de piano.

### Panneau de contrôle (GUI à droite)

| Paramètre          | Description                                    |
|--------------------|------------------------------------------------|
| **Gain**           | Volume global (0.0 → 1.0)                      |
| **Brillance**      | Richesse harmonique (1 → 15)                   |
| **Oscillator Mode**| `square` / `saw` / `sinus` / `piano`           |
| **Polyphony Mode** | Poly = jusqu'à 10 notes simultanées            |
| **Filter Active**  | Active/désactive le filtre passe-bande         |
| **Filter Frequency** | Fréquence de coupure du filtre (20–5000 Hz)  |
| **Filter Quality** | Résonance du filtre (facteur Q)                |
| **Filter Gain**    | Gain du filtre en dB (−20 → +20)               |

---

## Fonctionnalités

- **4 formes d'onde** : sinusoïde, carré, dent de scie, simulation piano (avec enveloppe de décroissance)
- **Polyphonie** : jusqu'à 10 voix simultanées grâce à un pool d'oscillateurs
- **Filtre audio** : filtre bi-quad en temps réel avec contrôle fréquence / qualité / gain
- **Visualisation spectrale** : transformée de Fourier appliquée au buffer audio
- **Interface graphique** : sliders et toggles via `ofxGui`, repositionnés dynamiquement

---

## Structure du code

```
.
├── main.cpp        — Point d'entrée, création de la fenêtre (1024×768)
├── ofApp.cpp/h     — Boucle principale : setup, draw, gestion audio et GUI
├── Keyboard.cpp/h  — Dessin et état du clavier virtuel, mapping touche→fréquence
├── Oscillo.cpp/h   — Oscillateur individuel : génération de la forme d'onde, enveloppe
└── Filter.cpp/h    — Filtre bi-quad (biquad IIR) appliqué au mix final
```

---


## Dépendances

| Dépendance       | Version testée | Rôle                          |
|------------------|----------------|-------------------------------|
| OpenFrameworks   | 0.12.0         | Fenêtre, audio, rendu         |
| ofxGui           | inclus dans OF | Sliders et toggles interactifs|
| g++ / MSVC       | C++17          | Compilation                   |
