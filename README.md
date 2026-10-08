# Apocalypter — Patch français

Patch de traduction française pour **Apocalypter**, fourni avec un installateur Windows. Le programme permet d'appliquer la traduction et de restaurer les fichiers anglais à partir de la sauvegarde créée lors de l'installation.

**Version de test :** le patch cible des fichiers du jeu utilisant Unity **2020.3.49f1**. Le rendu de la traduction reste à vérifier dans le jeu.

## Télécharger le patch

[Télécharger Apocalypter_Patch_FR.zip](Apocalypter_Patch_FR.zip)

L'archive contient l'installateur, les données de traduction, les sources et un fichier `LIRE-MOI.md`.

## Prérequis

- Windows avec **.NET Framework 4.5 ou ultérieur**.
- Une installation du jeu contenant `Apocalypter.exe` et `Apocalypter_Data/data.unity3d`.
- Plusieurs Go d'espace libre sur le disque du jeu pour les fichiers temporaires et la sauvegarde.

Python n'est pas nécessaire pour installer le patch.

## Installer la traduction

1. Fermer Apocalypter.
2. Extraire **tout le contenu du ZIP** dans un dossier, par exemple sur le Bureau.
3. Lancer `Apocalypter_Patch_FR.exe` depuis ce dossier.
4. Sélectionner le dossier qui contient `Apocalypter.exe`.
5. Cliquer sur **Installer le francais** et attendre le message de fin.
6. Lancer le jeu normalement.

Le traitement peut durer plusieurs minutes. Le journal de l'installateur permet de suivre son avancement.

Conserver `SharpCompress.dll`, `patch-fr.dat.gz` et `Apocalypter_Patch_FR.exe.config` à côté de l'EXE. Ne pas lancer l'installateur directement depuis le ZIP.

## Restaurer l'anglais

Fermer le jeu, relancer l'installateur, sélectionner le dossier du jeu puis cliquer sur **Restaurer l'anglais**.

Les fichiers de sauvegarde se trouvent dans `Apocalypter_Data` :

- `data.unity3d.avant-fr` : copie du fichier anglais d'origine.
- `data.unity3d.etat-fr` : empreintes utilisées pour vérifier la restauration.

Conserver ces deux fichiers, y compris après une restauration.

## Contenu et limites de la traduction

- Le patch annonce **1 791 occurrences de texte traduites** dans `level0`, `level1` et `sharedassets1.assets`.
- Les textes du jeu sont **sans accents** et les polices existantes sont conservées.
- L'installateur ne modifie pas `Assembly-CSharp.dll`.
- Le rapport `VERIFICATION.json`, inclus dans le ZIP, décrit les vérifications réalisées sur les fichiers et des bundles de test. Il indique que **le jeu n'a pas été lancé pour valider le patch** et que le bundle original complet n'était pas disponible.

La compatibilité avec d'autres versions du jeu n'est pas confirmée.

## Sources incluses

L'archive fournit également :

- `textes-fr.json` : les textes de la traduction.
- `source/PatchCore.cs` et `source/Program.cs` : le moteur du patch et l'interface Windows.
- `source/compiler.bat` : le script de compilation Windows.
- `source/regenerer_patch.py` et `source/requirements.txt` : les outils de régénération des données du patch, qui nécessitent des fichiers originaux du jeu.
- `LICENSE-SharpCompress.txt` : la licence de la bibliothèque SharpCompress utilisée par l'installateur.
