# Mini-projet Fruit Ninja VR — Partie 1

**Reprise et déploiement multiplateforme**  
Membres du groupe : à compléter  
Projet source : https://github.com/LeBN/FruitNinja_SysDev_Project  
État de la rédaction : 17 novembre 2026

## 1. Reprise et vérification du projet S7

### 1.1. Projet de départ

Nous avons repris le projet Fruit Ninja VR développé en S7 depuis son dépôt GitHub, en le récupérant localement avec GitHub Desktop. Le commit de référence lors de cette reprise est `1bc502b`, intitulé `Update SampleScene.unity`.

Le dépôt contient les dossiers `Assets`, `Packages` et `ProjectSettings`, nécessaires à l'ouverture du projet Unity. La scène de jeu utilisée est `Assets/Scenes/SampleScene.unity`. Elle est activée dans la liste de scènes de build examinée. Elle contient notamment un environnement forestier, un `XR Origin (XR Rig)`, un `XR Interaction Manager`, un gestionnaire de partie et un gestionnaire de lancement des fruits.

Les éléments hérités comprennent la scène, l'environnement, les modèles et prefabs, ainsi que les scripts du gameplay. À ce stade, notre travail porte sur la remise en route, la migration de l'environnement et la préparation des tests sans casque. Les scripts de gameplay n'ont pas encore été corrigés dans le cadre de cette reprise.

### 1.2. Environnement technique et migration

Le fichier `ProjectVersion.txt` indiquait une version d'origine Unity `6000.2.7f2`. Nous avons ouvert le projet avec Unity `6000.4.2f1`, déjà installé sur le poste, puis accepté sa mise à niveau. Les versions ci-dessous ont été relevées dans les fichiers du dépôt de référence et dans les fichiers locaux après migration.

| Élément | Version d'origine | Version utilisée après migration |
| --- | --- | --- |
| Éditeur Unity | `6000.2.7f2` | `6000.4.2f1` |
| URP — package Universal | `17.2.0` | `17.4.0` |
| XR Interaction Toolkit | `3.2.1` | `3.4.0` |
| XR Plug-in Management | `4.5.2` | `4.5.2` |
| Oculus XR Plugin | `4.5.2` | `4.5.4` |
| XR Hands | `1.6.1` | `1.7.3` |
| Input System | `1.14.2` | `1.19.0` |
| Cinemachine | `3.1.2` | `3.1.2` |

Le projet utilise le pipeline de rendu URP. Deux assets de configuration de rendu, `PC_RPAsset` et `Mobile_RPAsset`, sont présents. Leur présence ne valide pas encore les réglages des futurs builds.

Le manifeste actuel contient le plugin Oculus ; aucune dépendance directe au plugin OpenXR n'y a été relevée. La configuration OpenXR demandée par le sujet reste donc à effectuer et à vérifier pour les plateformes Windows et Android.

**Capture à intégrer :** version Unity utilisée et packages XR du projet.

### 1.3. Ouverture et diagnostic de la Console

Lors du premier chargement, Unity a signalé une interruption du téléchargement de Cinemachine `3.1.2`. Le journal du Package Manager contient le message `Error: aborted`. L'éditeur a ensuite pu s'ouvrir et la scène `SampleScene` a été chargée. Ce message ne suffit pas à établir une incompatibilité de Cinemachine avec la version de Unity utilisée.

La capture de Console réalisée après l'importation montre 54 avertissements et aucune erreur rouge. Plusieurs avertissements concernent des polygones auto-intersectants, écartés lors de l'importation des modèles du pack `JTS_TheSamuraiSword`. Leur impact sur les sabres effectivement utilisés devra être vérifié visuellement. Un autre avertissement signale un nœud ancien dans un Shader Graph HDRP fourni avec ce pack ; son utilisation effective dans ce projet URP reste à déterminer.

Les messages de création des réglages XR et de notice `Readme` introuvable n'ont pas empêché l'ouverture de la scène. Les avertissements n'ont pas été considérés comme corrigés après effacement de la Console : cette opération a seulement permis d'isoler les nouveaux messages au lancement.

En mode Play, Unity a signalé `Unable to start Oculus XR Plugin`. Aucun casque n'étant disponible lors de ces essais, l'initialisation du fournisseur Oculus et le suivi réel n'ont pas pu être validés.

**Capture à intégrer :** Console après importation, avec les compteurs et un exemple d'avertissement représentatif.

### 1.4. Tests exploratoires sans casque

Pour poursuivre les essais sur le PC Windows sans casque, nous avons importé le sample `XR Interaction Simulator` fourni avec XR Interaction Toolkit `3.4.0`, puis utilisé le simulateur dans la scène existante.

Les essais manuels ont permis de se déplacer avec ZQSD, de modifier la hauteur avec A/E et de tourner la vue avec les flèches. Le changement de mode avec Tab a également été essayé et paraît fonctionner. Ces premiers essais confirment la possibilité d'explorer la scène avec des entrées simulées. La manipulation indépendante des deux sabres et les événements de gameplay doivent encore être testés méthodiquement.

| Fonctionnalité ou élément | État observé | Intervention réalisée ou suite prévue |
| --- | --- | --- |
| Ouverture dans Unity | Projet et scène ouverts ; aucune erreur rouge visible sur la capture de contrôle | Migration vers `6000.4.2f1` et ouverture de `SampleScene` réalisées |
| Déplacements et rotation simulés | Fonctionnels lors des premiers essais manuels | Import et mise en service du simulateur réalisés |
| Changement de mode avec Tab | Essayé ; fonctionnement apparent | À compléter par un test distinct de chaque contrôleur |
| Suivi réel du casque et des contrôleurs | Non testé ; initialisation Oculus en échec pendant l'essai sans casque | Vérification ultérieure sur matériel et configuration OpenXR |
| Deux sabres et coupes | Non validés à ce stade | Tester chaque sabre sur le fruit de démarrage puis sur un fruit lancé |
| Fruits, bombes, bonus et score | Fonctionnement complet non validé | Réaliser un parcours de test ; vérifier le problème de sélection des bombes repéré dans le code |
| Difficultés, entraînement, menu et volume | Non validés | Vérifier les fonctions héritées et relever les éléments absents ou incomplets |
| Classement et sauvegarde JSON | Non validés ; le script `GameManager` examiné utilise `PlayerPrefs` pour un meilleur score | Vérifier les fonctions de classement et de sauvegarde attendues |
| Modèles de sabres et shader du pack | Avertissements d'importation présents | Contrôler les assets réellement utilisés et leur rendu |

**Capture à intégrer :** vue Game avec l'interface du simulateur dans `SampleScene`.

### 1.5. Points identifiés et limites de cette première étape

L'examen des scripts a permis de repérer plusieurs points à reprendre. Dans `FruitSpawnerManager`, le choix initial d'une bombe peut être remplacé par le bloc qui sélectionne un fruit ordinaire ou doré. Cette logique doit être corrigée, puis l'apparition des bombes testée. Le comportement spécifique des fruits bonus reste à vérifier.

Le script `SlideObject` contient des références à des effets de jus et d'explosion. Le jus est actuellement créé depuis le centre du fruit avec un décalage vertical ; son placement devra être adapté pour répondre à l'exigence d'un déclenchement au point de contact. L'existence de ces références ne valide pas encore les deux effets ni leurs variantes PCVR et Quest.

Enfin, la touche R est utilisée à la fois par le simulateur pour réinitialiser un appareil et par `GameManager` pour recréer le fruit de départ. Ce conflit doit être pris en compte pendant les prochains essais.

À l'issue de cette étape, le projet peut être ouvert et exploré avec le simulateur. La base jouable complète exigée par la Task 1 reste à valider : démarrage, manipulation des deux sabres, lancement et coupe des fruits, bombes, bonus et mise à jour du score. Aucun test sur casque, aucun build externe et aucune mesure de performance n'ont encore été réalisés dans cette reprise.

Les prochaines sections du rapport seront complétées après l'audit de l'organisation, l'adaptation des particules, la génération des builds et l'analyse de l'APK.
