# Executer le projet

Téléchargez le projet. Sur votre terminal dans le dossier racine, executez ces deux commandes :

`docker-compose build`

`docker-compose up -d`

Une fois les services en statut 'running', allez sur votre navigateur. Pour accéder au formulaire employée copiez cette route :

http://localhost:5000/

Vous pourrez également accéder à Prometheus avec ce lien :

http://localhost:9090/

à Prometheus PushtoGateway sur celui ci :

http://localhost:9091/

et Grafana :

http://localhost:3000/dashboards

Allez dans le dossier General puis dans le dossier Projet Attrition. Cela peut prendre un peu de temps pour que la visualisation s'affiche. Il faut d'abord que l'entrainement se termine et puisse exposer les données.


# 🦾 Rapport sur le modèle Spark ML

## Introduction
Le but de ce rapport est de présenter les différentes étapes qui ont été effectuées pour construire un modèle de Machine Learning avec Spark. Le modèle a été développé pour résoudre un problème lié à l'attrition des employées.

## Visualisation et analyse
La première étape consistait à explorer les données pour comprendre l'inégalité entre les classes. Nous avons utilisé différentes techniques de visualisation pour étudier la distribution des données et identifier les variables les plus importantes.

## Nettoyage des données
Après l'analyse des données, nous avons procédé à une étape de nettoyage. Cette étape comprenait :
Enlever les valeurs aberrantes Convertir les données dans leur bon type Enlever les colonnes avec des corrélations trop fortes, trop faibles ou null Modélisation Nous avons ensuite procédé à la modélisation. Nous avons utilisé la vectorisation des données qui ont été utilisées comme features et la colonne cible. Nous avons testé différents algorithmes en faisant du fine tuning sur les hyperparamètres pour avoir le modèle le plus pertinent.

## Evaluation du modèle
Enfin, nous avons regardé les métriques d'évaluation pour garder le modèle le plus efficace.

# 📈 Rapport sur le monitoring

## UPDATE MONITORING - Résolution du problème

Nous avons finalement réussi à récupéré les metrics de notre train en utilisant push_to_gateway , qui expose nos metrics sur le port 9091. Nous avons mis en écoute ce port dans les configurations de Prometheus et les données de notre metric d'évaluation remonte bien. ⚠️ L'entrainement des modèles prend un peu de temps à tourner, nous vous recommandons d'attendre environ 15-20min avant de pouvoir apprécier les metrics d'évaluation.

Ecran pushgateaway : 
https://drive.google.com/file/d/1cBIX_tjjzvKVcMdp16AHZnbx0uGNFsVx/view

Suivi de l'évaluation par modèle :
https://drive.google.com/file/d/1VzPEd_EaHoWGqenr0tPJ0-m65YgmnomQ/view

## MONITORING DU PORT 4040 - Difficultés
Pour surveiller les données d'entrainement, notre choix s'est porté sur l'application Prometheus et Grafana pour la visualisation.
L'idée était d'écouter le fichier train.py et de récupérer l'accuracy du modèle en exposant les données vers Prometheus.
Plusieurs difficultés se sont posés lors de l'initialisation notamment pour écouter les données du fichier train.py.
Nous avons configuré le fichier prometheus.yml pour qu'il puisse écouter les données issues du port 4040 (port du train)
N'étant pas dans le même container, nous avons exposé le port en modifiant le Dockerfile.train pour qu' `EXPOSE 4040` puisse permettre l'écoute. 
Nous avons également modifié le 'targets' du fichier prometheus.yml pour que celui ci écoute le port 4040 en initialisant d'abord : 
`train:4040` puis `host.docker.internal:4040` en voyant que la première commande ne marche pas.

Concernant l'exposition des données, nous avons utilisé le client python pour Prometheus qui nous semblait le moyen le plus simple d'exporter nos données.
`prometheus_client`.

Cependant lorsque nous voulions visualiser les metrics exportés. Nous remarquions qu'il y avait l'erreur `expected a valid start token, got "<" ("INVALID") while parsing: "<"` qui revenait assez souvent. Nous n'avons pas encore trouvé de solution à ce problème mais nous investiguons la dessus. Cela semble provenir du format d'exposition Prometheus mais, étant donné que nous utilisons un client python qui devrait normalement gérer le formatage des données vers Prometheus , il se peut également que le problème vienne de notre fichier train.py qui n'expose pas les données en continue et qui s'arrête lorsque l'entrainement du modèle se termine.

Nous cherchons actuellement une solution en regardant comment utiliser la fonction `push_to_gateway` pour exposer nos données sur un autre port de manière temporaire pour que Prometheus puisse les récupérer.

Nous aimerions et espérons avoir vos retours concernant ce problème.

⚠️ nous avons résolu le problème ⬆ à voir le paragraphe du haut.


# 📊 Rapport sur la visualisation

## Grafana

Nous avons décidé d'utilisé Grafana pour la visualisation et pour représenter notre accuracy pour chacun des modèles : gbt, rf et lr.

Nous avons utilisé la visualisation "Bar Gauge" qui nous permet rapidement d'apprécier les différences entre nos 3 modèles.
https://drive.google.com/file/d/1HJAv1clcHnvXQq9-prd_LZQOizMDD5H9/view

# Conclusion
En conclusion, nous avons présenté les différentes étapes qui ont été effectuées pour construire un modèle de Machine Learning avec Spark. Nous avons utilisé des techniques de visualisation et d'analyse pour comprendre le problème et nettoyé les données pour préparer le modèle. Nous avons ensuite utilisé la vectorisation des données pour la modélisation et testé différents algorithmes en faisant du fine tuning sur les hyperparamètres. Enfin, nous avons regardé les métriques d'évaluation pour garder le modèle le plus efficace. Nous avons exposés nos données à l'aide de prometheus et créé un dashboard avec un panel pour visualiser l'accuracy de nos modèles.

# Projet réalisé par :
Valentin GUERARD, Ngoc Thao LY, Gabriello ZAFIFOMENDRAHA, Thomas MERCIER, Gaëtan ALLAH ASRA BADJINAN
