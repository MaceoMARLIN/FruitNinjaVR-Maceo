# Fruit Ninja VR — Partie 1

**Brouillon de travail commencé le 17 novembre 2026.** Ce document doit être complété avec les résultats des tests et les captures avant export en PDF.

Ce fichier sert de journal et de liste de suivi. La première section rédigée pour le rapport se trouve dans [Rapport_Part1.md](Rapport_Part1.md).

- Membres du groupe : à compléter.
- Dépôt source : https://github.com/LeBN/FruitNinja_SysDev_Project
- Sujet : « Mini projet VR - Part 1 - Fruit Ninja VR.pdf ».
- Référence du dépôt lors de la reprise : commit `1bc502b` (`Update SampleScene.unity`).
- Matériel de test : PC Windows ; processeur, RAM, GPU et version de Windows à relever.
- Casque : aucun casque disponible lors des essais actuels.

## 1. Objectif et avancement

La partie 1 demande la reprise du gameplay S7, l'organisation du projet, deux types d'effets Particle System déclinés pour PCVR et Quest, deux builds testés sur matériel et une analyse de l'APK. Les livrables sont le projet source sur GitHub avec une notice, les builds Windows et Quest, et un rapport PDF synthétique.

| Travail demandé | État au 17 novembre 2026 |
| --- | --- |
| Task 1 — Reprise et vérification | En cours : ouverture et déplacements avec simulateur confirmés ; gameplay à tester. |
| Task 2 — Organisation | Première inspection réalisée ; références, prefabs et organisation à auditer. |
| Task 3 — Particules PCVR et Quest | Des références d'effets existent dans les scripts ; fonctionnement et variantes à vérifier. |
| Task 4 — Builds et tests | Aucun build produit ou testé dans cette reprise. |
| Task 5 — Analyse de l'APK | À réaliser après production de l'APK. |

## 2. État initial et environnement

Les versions d'origine proviennent des fichiers du commit de référence. Les versions actuelles proviennent des fichiers locaux après migration.

| Élément | Origine S7 | État actuel |
| --- | --- | --- |
| Unity | `6000.2.7f2` | `6000.4.2f1` |
| Pipeline de rendu | URP, packages `17.2.0` pour Core et Universal | URP, packages `17.4.0` ; assets `PC_RPAsset` et `Mobile_RPAsset` présents |
| XR Interaction Toolkit | `3.2.1` | `3.4.0` |
| XR Plug-in Management | `4.5.2` | `4.5.2` |
| Oculus XR Plugin | `4.5.2` | `4.5.4` |
| XR Hands | `1.6.1` | `1.7.3` |
| Input System | `1.14.2` | `1.19.0` |
| Cinemachine | `3.1.2` | `3.1.2` |
| OpenXR | Pas de dépendance directe dans le manifeste examiné | Pas de dépendance directe dans le manifeste examiné ; configuration demandée par le sujet à effectuer |
| Scène configurée dans la liste de build | `Assets/Scenes/SampleScene.unity` | Même scène, activée |

La scène contient notamment `GameManager`, `FruitSpawnerManager`, `XR Origin (XR Rig)`, un environnement forestier, des objets de menu, des fruits et des bombes. Leur présence ne constitue pas une validation de leur fonctionnement.

Une copie initiale a été recommandée ; sa réalisation reste à confirmer. La branche locale observée lors de cet état des lieux est `main`. Créer une branche de travail dans GitHub Desktop avant les prochaines corrections et conserver les modifications actuelles.

## 3. Manipulations réalisées pendant cette reprise

1. Récupération du dépôt avec GitHub Desktop.
2. Lecture de la version d'origine dans `ProjectSettings/ProjectVersion.txt`.
3. Ouverture avec Unity `6000.4.2f1` et acceptation de la migration.
4. Diagnostic d'une interruption du téléchargement de Cinemachine `3.1.2` : le journal Package Manager indique `Error: aborted`. L'éditeur a ensuite pu s'ouvrir ; aucune suppression de Cinemachine n'a été effectuée par l'assistant.
5. Ouverture de `SampleScene`, après affichage initial d'une scène vide `Untitled`.
6. Lecture des avertissements d'importation ; la capture de Console fournie à ce stade montre 54 avertissements et 0 erreur rouge.
7. Effacement de l'affichage de la Console, puis lancement du mode Play. Le message signalé à cette étape concerne l'initialisation du plugin Oculus.
8. Import du sample `XR Interaction Simulator` de XR Interaction Toolkit `3.4.0`.
9. Mise en service du simulateur dans l'éditeur : l'utilisateur confirme les déplacements avec ZQSD, la rotation avec les flèches et le déplacement vertical avec A/E. Il indique également avoir essayé Tab et les outils du simulateur, avec un fonctionnement apparent. La manipulation indépendante des deux sabres et le fonctionnement après fermeture et réouverture de Unity restent à vérifier.

Aucune correction des scripts de gameplay n'a été réalisée par l'assistant à ce stade.

## 4. Avertissements et problèmes à suivre

| Message ou constat | Interprétation et impact | Action / état |
| --- | --- | --- |
| Polygones auto-intersectants écartés dans plusieurs modèles `JTS_TheSamuraiSword` | Des polygones ont été supprimés à l'importation. L'impact visuel sur les sabres réellement utilisés doit être examiné. | À contrôler visuellement ; ne pas déclarer corrigé. |
| Nœud ancien du Shader Graph `jts_custom_sword_HDRP.shadergraph` | Avertissement de migration d'un shader fourni avec le pack. Son utilisation effective dans ce projet URP reste à vérifier. | Identifier les matériaux utilisés avant toute modification. |
| `Created initial copy of settings: XRInteractionRuntimeSettings` | Message de création de réglages XR. | Information, pas une erreur de gameplay. |
| `Couldn't find a readme` | Message du script d'éditeur `ReadmeEditor` de `TutorialInfo`. | Aucun blocage du jeu observé lié à ce message. |
| `Unable to start Oculus XR Plugin` | L'initialisation du fournisseur Oculus échoue. Le journal propose notamment un casque absent ou un runtime indisponible. | Aucun casque disponible ; suivi réel et runtime non validés. |
| `Cinemachine: aborted` lors du premier chargement | Téléchargement interrompu dans le journal UPM. | Ouverture ultérieure obtenue ; réussite du téléchargement à confirmer si le message revient. |

Le bouton **Clear** efface les messages affichés. Il ne corrige pas leur cause. Les avertissements d'importation peuvent réapparaître après une réimportation ; les avertissements d'exécution peuvent revenir à chaque Play.

## 5. Premiers constats à la lecture du code

Ces constats résultent d'une inspection des scripts ; ils doivent être rapprochés des essais en jeu. Les corrections ne sont pas encore réalisées.

- **Choix des bombes :** dans `FruitSpawnerManager.SpawnRandomObject`, le choix d'une bombe est suivi d'un second `if` pour le fruit doré, avec un `else if` pour les fruits ordinaires. Lorsque le tableau de fruits est rempli, ce second bloc remplace le choix de bombe. Revoir les branches de sélection, puis tester l'apparition des bombes.
- **Sauvegarde :** `GameManager` stocke un meilleur score avec `PlayerPrefs`. Cela ne prouve pas l'existence du classement et de la sauvegarde JSON évoqués par le sujet ; ceux-ci n'ont pas été repérés dans les scripts de jeu examinés.
- **Fruit bonus :** un prefab de fruit doré est prévu par le spawner. Le chemin de coupe ordinaire de `SlideObject` attribue `+1`. Un comportement bonus distinct reste à identifier ou à compléter.
- **Position des effets :** `SlideObject` utilise le centre de la cible pour l'explosion et un décalage vertical depuis le centre pour le jus. Le sujet demande un effet de coupe au point de contact : ce placement est à adapter.
- **Raccourci R :** le simulateur utilise R pour réinitialiser un appareil, tandis que `GameManager.Update` utilise aussi R pour recréer le fruit de démarrage. Tenir compte de ce conflit pendant les essais et prévoir un réglage adapté.

## 6. Parcours de tests à compléter

Statuts possibles : confirmé dans l'éditeur, anomalie observée, non testé. Un résultat en simulation ne valide ni le suivi réel ni l'exécution sur Quest.

| Vérification | Éditeur avec simulateur | Windows PCVR avec casque | Quest autonome |
| --- | --- | --- | --- |
| Ouverture de la scène | Confirmée par capture | Non testé | Non testé |
| Déplacements et rotation simulés | Confirmés par l'utilisateur | Sans objet | Sans objet |
| Changement de mode avec Tab | Essayé par l'utilisateur ; fonctionnement apparent | Sans objet | Sans objet |
| Deux contrôleurs et deux sabres manipulables | Non testé | Non testé | Non testé |
| Démarrage par le fruit de départ | Non testé | Non testé | Non testé |
| Lancement et coupe des fruits | Non testé | Non testé | Non testé |
| Mise à jour du score après une coupe | Non testé | Non testé | Non testé |
| Apparition des bombes et pénalité | Non testé ; problème de sélection repéré dans le code | Non testé | Non testé |
| Fruits bonus et comportement associé | Non testé | Non testé | Non testé |
| Chronomètre et fin de partie | Non testé | Non testé | Non testé |
| Menu, difficultés et entraînement | Non testé | Non testé | Non testé |
| Volume, classement et sauvegarde | Non testé | Non testé | Non testé |
| Jus de fruit et explosion | Non testé | Non testé | Non testé |
| Absence de crash sur un parcours complet | Non testé | Non testé | Non testé |
| Fluidité / FPS mesurés | Aucune mesure | Non testé | Non testé |

Pour chaque essai, noter la date, la procédure, le résultat réel et les erreurs éventuelles. Indiquer l'outil et les conditions de mesure pour toute valeur de FPS.

## 7. Organisation et effets à documenter

Les dossiers `Scenes`, `Scripts`, `Prefabs`, `Materials`, `Resources`, `Sounds` et plusieurs packs de ressources existent déjà. L'audit des références, composants manquants et assets à la racine reste à faire. Effectuer les déplacements et renommages dans Unity.

| Effet demandé | Prefab PCVR | Prefab Quest | Paramètres et observations |
| --- | --- | --- | --- |
| Coupe / jus de fruit | À identifier ou créer | À créer ou identifier | Nombre de particules, durée de vie, matériau, modules, placement et nettoyage à relever |
| Explosion de bombe | À identifier ou créer | À créer ou identifier | Mêmes paramètres ; déclenchement et lien avec la pénalité à tester |

Documenter le mécanisme qui sélectionne la variante selon la plateforme. Joindre des captures comparables en situation de jeu. La seule présence des assets de rendu PC et Mobile ne prouve pas l'existence de variantes de particules.

## 8. Builds et analyse à réaliser

- Windows : relever la configuration OpenXR, le runtime, les scènes et la configuration graphique ; produire le build complet et le tester hors de l'éditeur avec un casque.
- Quest : relever les modules Android, SDK/NDK, IL2CPP, ARM64, API Android, options graphiques et fonctionnalités OpenXR/Quest ; produire l'APK, l'installer et le tester en autonome.
- APK Analyzer : relever la taille de l'APK, ses grandes catégories, les données Unity et `libil2cpp.so` lorsque présents ; joindre les captures et préciser les limites d'identification des ressources regroupées dans les fichiers Unity.
- Comparaison : relever séparément la taille de l'APK, du ZIP Windows et du dossier Windows complet, avec unités et prise en compte de la compression. Comparer ensuite qualité et fluidité réellement observées.

**Limitation actuelle : aucun test sur casque et aucun résultat de performance matérielle.** Signaler les essais non réalisés dans le rapport final si cette limitation persiste.

## 9. Captures et livrables

Captures déjà fournies pendant la reprise, à conserver séparément pour le PDF : version d'origine, confirmation de migration, scène chargée, Console après import, sample du simulateur importé. Ajouter les vues en simulation, les comparaisons d'effets et les résultats des builds lors des prochaines étapes. Les images ne sont pas encore intégrées à ce brouillon.

- [ ] Confirmer la copie de sauvegarde initiale et créer une branche de travail.
- [ ] Compléter le tableau fonctionnalité / état observé / correction.
- [ ] Vérifier les références des scènes et prefabs.
- [ ] Produire les variantes de particules et leurs captures.
- [ ] Tester et archiver le build Windows complet et l'APK Quest.
- [ ] Réaliser l'analyse APK et la comparaison des tailles.
- [ ] Ajouter une notice au dépôt : version Unity, scène de démarrage, contrôles et lancement.
- [ ] Compléter les membres du groupe, le matériel et les limitations.
- [ ] Exporter le rapport final en PDF après vérification des résultats.
