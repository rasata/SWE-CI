# SWE-CI : Évaluation des capacités des agents à maintenir des bases de code via l'intégration continue

<p align="center">
  [简体中文] | [English] | Français
</p>

🔗 Lien HuggingFace : https://huggingface.co/datasets/skylenage/SWE-CI

🔗 Lien vers l'article : À paraître...

## Introduction
![](docs/1.png)



### 🏆 Qu'est-ce que SWE-CI ?

La maintenabilité du code est essentielle dans le cycle de vie logiciel. SWE-CI est le premier benchmark spécifiquement conçu pour évaluer la *capacité des agents IA à maintenir un dépôt de code*. L'idée clé de SWE-CI est la suivante : **une bonne maintenance ne se limite pas à garantir le bon fonctionnement du code actuel, mais vise également à réduire la difficulté de développement nécessaire pour que le code continue à fonctionner correctement à l'avenir.**

SWE-CI a sélectionné 100 paires de versions de commits de haute qualité provenant de GitHub. Chaque paire comprend un code de base et un code de référence, issus de différentes périodes d'un même dépôt. SWE-CI demande aux agents IA de maintenir le code à partir de la version de base, avec pour objectif de passer tous les tests du code de référence. En quantifiant le degré auquel la séquence d'évolution du code maintient continuellement sa correction fonctionnelle, SWE-CI peut mesurer efficacement la capacité des agents IA à maintenir du code.

SWE-CI introduit un **workflow collaboratif à deux agents** unique, simulant la boucle d'intégration continue (boucle CI) d'une équipe logicielle professionnelle réelle :

- **Agent architecte (Architect Agent)** : Responsable de l'analyse des informations de test fournies par le système de tests automatisés. Par l'attribution des échecs, la localisation du code et la conception des exigences, il produit des documents d'exigences professionnels et de haut niveau en langage naturel, qui sont transmis au programmeur pour le développement.

- **Agent programmeur (Programmer Agent)** : Après réception du document d'exigences, l'agent programmeur traduit les exigences du document en spécifications de comportement de code explicites, planifie le plan de maintenance du code et implémente finalement le codage.

En exécutant de manière répétée le cycle fermé **« Exécuter les tests → Définir les exigences → Modifier le code »**, SWE-CI simule efficacement les cycles d'itération de développement logiciel du monde réel, fournissant une nouvelle plateforme pour mesurer systématiquement la capacité globale des agents à maintenir des bases de code **à long terme**.

### 🏆 Pourquoi avons-nous besoin de SWE-CI ?

Par rapport aux benchmarks précédents, SWE-CI introduit **trois changements fondamentaux** :

#### 1️⃣ Du « correctif ponctuel » au « suivi de l'évolution »

La plupart des benchmarks d'évaluation actuels suivent le paradigme de correction ponctuelle **« Issue → PR »** : étant donné un rapport de bug à un instant donné, le modèle doit effectuer la correction en une seule fois. Cependant, dans le monde réel, les tâches d'ingénierie logicielle ne sont presque jamais accomplies en une seule fois. SWE-CI ne se concentre plus sur la correction d'un bug unique, mais sur **la trajectoire d'évolution entre deux versions de commits** (du commit actuel au commit cible). Il reproduit fidèlement le processus dynamique de croissance, de refactorisation et d'évolution continue d'une base de code au fil du temps.

#### 2️⃣ De la « description statique des exigences » à la « génération dynamique des exigences »

SWE-CI ne s'appuie pas sur des descriptions d'issues rédigées manuellement à l'avance, mais utilise plutôt **l'« écart de tests (Test Gap) »** entre le code actuel et le code de référence comme moteur principal de la génération de documents d'exigences. Dans l'ingénierie logicielle du monde réel, les exigences dépendent fortement de l'état actuel du code et sont difficiles à prédire à l'avance. Ce processus, en intégrant le flux de tests automatisés de l'intégration continue, permet la détection en temps réel des défauts fonctionnels du code et la génération automatisée des exigences.

#### 3️⃣ De « évaluer l'écriture de code correct » à « évaluer l'écriture de code maintenable »

SWE-CI ne s'intéresse pas uniquement à la capacité d'un agent à implémenter correctement les exigences en une seule tentative, mais aussi à savoir si cette correction peut être maintenue dans le futur. Grâce au suivi continu de la correction fonctionnelle des séquences de modifications du code, SWE-CI quantifie objectivement le concept flou de « capacité de maintenance » et fournit de nouvelles perspectives pour la construction de systèmes d'agents plus performants.


## Classement

![](docs/result.png)


Dans SWE-CI, nous utilisons le changement normalisé moyen (Average Normalized Change, ANC) pour mesurer la capacité du modèle à maintenir du code. Nous définissons les notations suivantes :

*   $p_i^{(j)}$ : le nombre de tests unitaires passés par le code de la tâche $j$ à l'itération $i$. $p_0^{(j)}$ représente le nombre de tests unitaires passés par le code initial de la tâche $j$ avant le début des itérations.
*   $p_{\ast}^{(j)}$ : le nombre total de tests unitaires à passer pour la tâche $j$, équivalent au nombre de tests unitaires passés par le code de référence (ground truth).
*   $N$ : le nombre maximum d'itérations.
*   $M$ : le nombre total de tâches dans le jeu de données.

Nous définissons d'abord le changement normalisé (Normalized Change, NC) comme l'amélioration relative (positive ou négative) par rapport à la situation de référence lors d'une itération donnée :

$$
a_i^{(j)}=\begin{cases}
\dfrac{p_i^{(j)}-p_0^{(j)}}{p_\ast^{(j)}-p_0^{(j)}}, & \text{si}\ p_i^{(j)} \geq p_0^{(j)}\\
\dfrac{p_i^{(j)}-p_0^{(j)}}{p_0^{(j)}}, & \text{si}\ p_i^{(j)} < p_0^{(j)}
\end{cases}
$$

Le changement normalisé moyen est alors défini comme :

$$
{\rm ANC} =\frac{1}{MN}\sum_{j=1}^M\sum_{i=1}^N a_i^{(j)}
$$

Cet indicateur prend en compte de manière globale les variations de la correction fonctionnelle au cours de l'ensemble du cycle de maintenance du code, servant ainsi de mesure fiable de la capacité de l'agent à maintenir du code.


## Démarrage rapide

### 🌍 Compatibilité
Le dépôt ne prend actuellement en charge que le système d'exploitation Linux et l'interface CLI iFlow. La prise en charge de Windows, de Claude Code CLI et d'OpenCode CLI sera ajoutée progressivement.

### 💰 Coût de référence
Dans l'environnement de test suivant, l'exécution de ce projet sur le jeu de données complet (full.csv) nécessite environ **48 heures** :
+ Configuration matérielle : CPU 32 cœurs, 64 Go de RAM, environ 1 Go/s de vitesse de lecture/écriture disque
+ Configuration de la concurrence : 16 processus simultanés
+ Clé API : une clé API LLM supportant au moins 16 requêtes simultanées.

### 🚀 Installation

**Étape 1 :** Ce dépôt est basé sur Docker. Avant la première exécution, veuillez vous assurer que Docker fonctionne correctement avec la commande suivante.
```bash
docker run hello-world
```
Idéalement, vous devriez voir le message "Hello from Docker!" dans la sortie. Vous pouvez consulter les instructions d'installation de Docker [ici](https://www.docker.com/get-started/).

**Étape 2 :** Téléchargez et installez le projet depuis GitHub. Par défaut, [Anaconda](https://www.anaconda.com/download) / [Miniconda](https://www.anaconda.com/docs/getting-started/miniconda/install) / [Miniforge](https://github.com/conda-forge/miniforge) est utilisé pour gérer l'environnement Python.
```bash
git clone https://github.com/Loong-Chan/SWE-CI.git
cd SWE-CI

conda create --name sweci python=3.11 -y
conda activate sweci
pip install -r requirements.txt
```

### 🏃 Exécution

**Télécharger le jeu de données depuis Hugging Face :** La première exécution nécessite de télécharger les données depuis Hugging Face. Le jeu de données complet (full.csv) nécessite environ 52,8 Go d'espace de stockage.
```bash
# (Recommandé) Télécharger avec les paramètres par défaut
PYTHONPATH=src python -m swe_ci.download

# (Personnalisé) Télécharger avec des paramètres personnalisés
# --splitting : optionnel, subdivision du jeu de données, valeur par défaut "full"
# --hf_token : optionnel, utilisé pour accélérer le chargement, valeur par défaut "none"
PYTHONPATH=src python -m swe_ci.download \
    --splitting <SPLITTING> \
    --hf_token <HF_TOKEN>
```

**Lancer l'expérience** :
+ Par défaut, vous pouvez passer tous les paramètres via la ligne de commande. Les paramètres `--api_key` / `--base_url` / `--model_name` sont compatibles avec le protocole d'interface OpenAI. Vous pouvez également définir `--iflow.auth_type` sur `iflow` pour utiliser le protocole d'interface iFlow. Pour plus de détails, consultez la [documentation officielle d'iFlow](https://platform.iflow.cn/docs).
+ Cette expérience comprend deux phases : *l'initialisation des tâches* et *l'évolution du code*. L'initialisation prend environ 30 minutes (avec une concurrence de 16). Lorsque les ressources système sont limitées, certaines tâches peuvent expirer lors de l'initialisation. Dans ce cas, réduisez les limites de ressources des conteneurs Docker ou diminuez la concurrence et relancez la commande. La phase d'évolution du code (environ 48 heures) ne commence qu'une fois toutes les tâches initialisées.
```bash
# --experiment_name obligatoire, chaîne identifiant de manière unique l'expérience,
#   la réutilisation du même experiment_name permet de reprendre l'exécution
# --splitting optionnel, valeur par défaut "full", doit correspondre au paramètre utilisé lors du téléchargement
# --api_key / --base_url / --model_name obligatoires
PYTHONPATH=src nohup python -u -m swe_ci.evaluate \
    --experiment_name <NOM_EXPERIENCE> \
    --splitting <SPLITTING> \
    --api_key <CLE_API> \
    --base_url <URL_BASE> \
    --model_name <NOM_MODELE> \
    > temp.log 2>&1 &
```
+ De manière plus pratique, vous pouvez modifier directement le fichier config.toml du projet et redéfinir les valeurs par défaut de n'importe quel paramètre, afin d'obtenir des configurations d'expérience plus fines et d'éviter de saisir les paramètres de manière répétitive en ligne de commande.
```bash
# En supposant que tous les champs obligatoires ont été définis dans config.toml
PYTHONPATH=src nohup python -u -m swe_ci.evaluate > temp.log 2>&1 &
```
+ Si vous avez besoin d'exécuter des expériences sous plusieurs configurations différentes, nous vous recommandons de créer un fichier de configuration distinct pour chaque groupe d'expériences et d'utiliser le paramètre `--config_file` pour spécifier votre fichier de configuration personnalisé.
```bash
# En supposant la création d'un nouveau fichier de configuration my_config_1.toml
# (doit se trouver dans le même répertoire que config.toml, avec les mêmes entrées de configuration)
# et que tous les champs obligatoires y ont été définis.
PYTHONPATH=src nohup python -u -m swe_ci.evaluate \
    --config_file my_config_1.toml \
    > temp.log 2>&1 &
```
⚠️ Étant donné la durée d'exécution de l'expérience (environ 48 heures avec 16 processus simultanés), nous vous recommandons de noter le PID de la commande après exécution afin de pouvoir arrêter le processus si nécessaire.

⚠️ Vous pouvez ajuster la concurrence et les limites de ressources des conteneurs Docker (CPU, mémoire et E/S) dans le fichier config.toml selon vos ressources disponibles.

⚠️ Il est normal que certaines tâches échouent en raison de situations imprévues (par exemple : dépassement de la limite de concurrence de la clé API, ou modifications inappropriées de l'agent entraînant un dépassement de délai). Dans la plupart des cas, relancer l'expérience suffit à résoudre le problème.

### 📄 Consulter les résultats de l'expérience
Vous pouvez consulter les résultats de l'expérience en spécifiant les paramètres `--experiment_name` et `--splitting`.
```bash
PYTHONPATH=src python -m swe_ci.summarize \
    --experiment_name <NOM_EXPERIENCE> \
    --splitting <SPLITTING>
```
