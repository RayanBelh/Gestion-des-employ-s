 # Gestion d'employés en Java - Projet NetBeans

## Description
Ce projet est une application Java simple qui permet de gérer des employés dans une entreprise en utilisant JDBC pour interagir avec une base de données MySQL. Elle permet la création, l'affichage, la mise à jour et la suppression d'employés (développeurs et managers) dans une base de données.

## Structure du code
Le code est organisé en plusieurs packages :

### 1. **`ma.projet.beans`**
- **`Personne.java`** : Classe abstraite qui définit les attributs et méthodes de base pour un employé.
- **`Developpeur.java`** : Classe héritant de `Personne`, représentant un développeur avec des attributs et méthodes spécifiques.
- **`Manager.java`** : Classe héritant de `Personne`, représentant un manager avec des attributs et méthodes spécifiques.

### 2. **`ma.projet.connexion`**
- **`Connexion.java`** : Classe qui gère la connexion à la base de données MySQL.

### 3. **`ma.projet.dao`**
- **`IDao.java`** : Interface définissant les méthodes CRUD (Create, Read, Update, Delete) pour un DAO (Data Access Object).

### 4. **`ma.projet.service`**
- **`DeveloppeurService.java`** : Classe implémentant l'interface `IDao` pour gérer les opérations CRUD sur les développeurs.
- **`ManagerService.java`** : Classe implémentant l'interface `IDao` pour gérer les opérations CRUD sur les managers.

### 5. **`ma.projet.test`**
- **`Entreprise.java`** : Classe principale qui contient la logique du programme, incluant la création et l'affichage des employés.

## Fonctionnement de l'application

### Connexion à la base de données
L'application se connecte à une base de données MySQL nommée **`entreprise`** sur `localhost` en utilisant l'utilisateur `root` avec un mot de passe `root`. Les informations de connexion sont stockées dans le fichier `base.properties` situé dans le package `ma.projet.connexion`.
