# Apocalypter — Patch français

Patch de traduction française pour **Apocalypter**, fourni avec un installateur Windows. Le programme permet d'appliquer la traduction et de restaurer les fichiers anglais à partir de la sauvegarde créée lors de l'installation.

**Version de test :** le patch cible des fichiers du jeu utilisant Unity **2020.3.49f1**. Le rendu de la traduction reste à vérifier dans le jeu.

## Télécharger le patch

[Télécharger Apocalypter_Patch_FR.zip](https://github.com/Daymato/Patch-FR---Apocalypter/raw/refs/heads/main/Apocalypter_Patch_FR.zip)

L'archive contient l'installateur, les données de traduction, les sources et un fichier `LIRE-MOI.md`.

## Prérequis

- Windows avec **[.NET Framework 4.5 ou ultérieur](https://dotnet.microsoft.com/download/dotnet-framework/net48)**.
- Une installation du jeu contenant `Apocalypter.exe` et `Apocalypter_Data/data.unity3d`.
- Plusieurs Go d'espace libre sur le disque du jeu pour les fichiers temporaires et la sauvegarde.

Python n'est pas nécessaire pour installer le patch.

Le lien Microsoft propose .NET Framework 4.8, compatible avec ce prérequis. Choisir le téléchargement **Runtime** pour exécuter le patch.

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

## Détails techniques

### Architecture et traitement des fichiers Unity

L'installateur est écrit en **C#**, avec une interface **Windows Forms** ciblant **.NET Framework 4.5**. `Program.cs` gère l'interface et les commandes ; `PatchCore.cs` assure la lecture des bundles, l'application du patch et la restauration. SharpCompress fournit le décodeur LZMA ; le code inclut son propre encodeur et décodeur LZ4.

Le fichier modifié est `Apocalypter_Data/data.unity3d`, un conteneur **UnityFS**. Le moteur accepte les versions de format UnityFS **6 à 8** et lit les blocs non compressés, LZMA ou LZ4/LZ4HC. Il refuse les indicateurs de chiffrement qu'il détecte. Ces capacités de lecture ne garantissent pas la compatibilité de la traduction avec une autre version du jeu : les assets ciblés doivent aussi correspondre aux empreintes attendues.

La reconstruction remplace les assets ciblés et copie le contenu des autres entrées. Le nouveau bundle utilise des blocs de **1 Mio**, compressés en LZ4 lorsque cela réduit leur taille, sinon conservés sans compression. Les offsets et la table des entrées sont recalculés ; le conteneur reconstruit peut donc différer du conteneur original, même pour les ressources dont le contenu est conservé.

### Format du patch et contrôles d'intégrité

`patch-fr.dat.gz` est un fichier binaire compressé en **gzip**. Une fois décompressé, il commence par la signature `AFRPAT01`, puis décrit les assets à modifier. Chaque asset possède :

- Son nom, ses tailles avant et après modification et ses empreintes **SHA-256** avant et après modification.
- Une liste de modifications binaires indiquant l'offset original, la longueur à remplacer et les nouveaux octets.

Le moteur vérifie la taille et le SHA-256 de chaque asset source avant de le modifier, puis contrôle ceux du résultat. Une incompatibilité interrompt la préparation avant le remplacement du fichier du jeu.

L'installation travaille dans un dossier temporaire créé à côté de `data.unity3d`. Elle vérifie également que le fichier du jeu n'a pas changé pendant le traitement, conserve la sauvegarde anglaise et enregistre les SHA-256 du bundle original et du bundle traduit dans `data.unity3d.etat-fr`. Sous Windows, le remplacement utilise `File.Replace` lorsque cette opération est prise en charge.

La restauration automatique compare la sauvegarde et le fichier courant à ces empreintes. Si les fichiers ont changé depuis l'installation, elle s'arrête pour éviter d'écraser une autre version du jeu.

### Modifier les textes et régénérer le patch

Les sources se trouvent **dans le ZIP**. Les commandes ci-dessous sont à lancer sous Windows, depuis le dossier `Apocalypter_Patch_FR` obtenu après extraction complète de l'archive.

`textes-fr.json` contient les textes sources, les traductions dans le champ `fr` et leurs occurrences, repérées par fichier, identifiant d'objet Unity (`path_id`) et chemin de champ. Pour corriger une traduction, modifier son champ `fr` en conservant les textes sources et les repères. Le générateur impose des traductions **ASCII**, donc sans accents.

La régénération nécessite Python 3, les assets **originaux extraits** (`level0`, `level1` et `sharedassets1.assets`) et les DLL originales du dossier `Apocalypter_Data/Managed`. Ces fichiers du jeu ne sont pas fournis dans cette archive.

Créer un environnement Python et installer les versions de dépendances déclarées :

```powershell
py -3 -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r .\source\requirements.txt
```

Le fichier de dépendances fixe **UnityPy 1.25.4** et **TypeTreeGeneratorAPI 0.0.10**. UnityPy lit les assets ; le générateur de type trees utilise les DLL originales pour retrouver la structure des objets sérialisés.

Régénérer les données en adaptant les deux chemins aux fichiers originaux disponibles :

```powershell
.\.venv\Scripts\python.exe .\source\regenerer_patch.py --extraits "C:\Apocalypter-original\extraits" --managed "C:\Apocalypter-original\Apocalypter_Data\Managed"
```

Par défaut, le script lit `textes-fr.json` et remplace `patch-fr.dat.gz` dans le dossier du patch. Les options `--textes` et `--sortie` permettent de choisir d'autres fichiers. Il contrôle les empreintes des assets originaux, vérifie leur sérialisation avant traduction et relit les objets traduits avant de produire le patch. Modifier le JSON seul ne met pas à jour les données utilisées par l'installateur.

### Recompiler et utiliser la ligne de commande

Pour recompiler l'installateur sous Windows :

```powershell
.\source\compiler.bat
```

Le script recherche `csc.exe` dans les dossiers .NET Framework de Windows et produit `Apocalypter_Patch_FR.exe` en **AnyCPU**, avec les références à Windows Forms, System.Drawing et SharpCompress. Le compilateur .NET Framework et `SharpCompress.dll` doivent être disponibles ; ce script ne les installe pas.

L'exécutable accepte aussi des commandes sans ouvrir l'interface graphique, toujours depuis le dossier extrait et avec le jeu fermé :

Installer la traduction :

```powershell
.\Apocalypter_Patch_FR.exe --install "C:\Jeux\Apocalypter" ".\patch-fr.dat.gz"
```

Restaurer l'anglais :

```powershell
.\Apocalypter_Patch_FR.exe --restore "C:\Jeux\Apocalypter"
```

Ces commandes utilisent le même moteur et les mêmes contrôles que les boutons de l'interface.
