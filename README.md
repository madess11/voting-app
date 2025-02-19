# Modernisation voting-app

## 1. Introduction

Ce document décrit les modifications apportées au projet, son fonctionnement, ainsi que le processus de déploiement sur un cluster Docker Swarm.

## 2. Modifications Apportées

### 2.1. Modification du Code Source

1) Utilisation du nom du service `db` pour accéder à la base de données au lieu de `localhost`.
2) Installation de la dépendance Node `dotenv` et utilisation d’un fichier `.env` pour l'application `result`. La connexion à PostgreSQL a été modifiée comme suit dans `./result/server.js` :

   ```javascript
   const pool = new pg.Pool({
     host: process.env.DB_HOST,
     port: process.env.DB_PORT,
     user: process.env.DB_USER,
     password: process.env.DB_PASSWORD,
     database: process.env.DB_NAME
   });
   ```

3) Suppression de la création de la table `votes` dans `./worker/program.cs` et déplacement de cette requête SQL dans le fichier d'initialisation `init.sql` du service `db`.

## 3. Fonctionnement de l’Application

L’application repose sur 5 services définis dans `compose.yaml` :

- **Vote** : Interface permettant aux utilisateurs de voter.
- **Worker** : Traitement des votes en arrière-plan.
- **Result** : Interface affichant les résultats des votes.
- **Redis** : Cache servant à stocker temporairement les votes.
- **PostgreSQL** : Base de données stockant les votes de manière persistante.

Les services sont reliés via les réseaux `front` et `back`, et certains services dépendent de `db` et `redis`, assurant une initialisation correcte.

## 4. Processus pour Lancer le Projet

1. **Cloner le dépôt du projet**

   ```bash
   git clone git@github.com:madess11/voting-app.git
   cd voting-app
   ```

2. **Créer un fichier `.env` pour la base de données à la racine du projet** en remplissant les variables nécessaires.

```
POSTGRES_USER=db_user
POSTGRES_PASSWORD=db_password
POSTGRES_DB=votes
```

3. **Créer un fichier `.env` dans le dossier result** en remplissant les variables nécessaires.

```
DB_HOST=db
DB_PORT=5432
DB_USER=db_user
DB_PASSWORD=db_password
DB_NAME=votes
```

4. **Construire et lancer les services avec Docker Compose**

   ```bash
   docker-compose up -d
   ```

5. **Accéder à l’application** :
   - Interface de vote : `http://localhost:8080`
   - Interface de résultats : `http://localhost:3000`

## 5. Mise en Place d’un Cluster Docker Swarm

### 5.1. Création du Cluster

1. **Initialiser le Swarm sur le nœud manager**

   ```bash
   docker swarm init --advertise-addr <IP DU MANAGER>
   ```

2. **Récupérer le token d’ajout des workers**

   ```bash
   docker swarm join-token worker
   ```

3. **Ajouter les nœuds workers** (exécuter sur chaque worker)

   ```bash
   docker swarm join --token <TOKEN> <IP DU MANAGER>:2377
   ```

4. **Vérifier le cluster**

   ```bash
   docker node ls
   ```

### 5.2. Déploiement avec Docker Swarm

1. **Copier le contenu du fichier `swarm-setup.yaml` dans le fichier  `compose.yaml`**.
2. **Déployer l’application**

   ```bash
   docker stack deploy -c compose.yaml voting-app
   ```

3. **Vérifier les services**

   ```bash
   docker service ls
   ```

## 6. Conclusion

Le passage à Docker Swarm permet une meilleure scalabilité et une gestion simplifiée du déploiement, garantissant un fonctionnement fiable et résilient.
