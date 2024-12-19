
# ArchiLivre

4 couches Applicatives 

## Description des couches et leur rôle

### Application : 
 - Description: met en œuvre et coordonne la logique de traitement et d’interaction, en contrôlant le flux de données entre les autres couches. Également connue sous le nom de couche de logique métier, cette couche est responsable de la gestion des opérations, des règles et des flux de travail de l'application
 - Role: Orchestration(Services), Gestion des Transactions(Contracts),logique spécifique à l'app(Services)

### Domaine : 
 - Description: logique métier 
 - Role: Règles/logique Métier, validation métier, cohérence des données

### Persistance : 
 - Description:gère les interactions et les communications avec les systèmes de stockage de données, tels que les bases de données et les services externes, en séparant les moyens par lesquels les données sont obtenues, stockées et mises à jour du reste de l'application.
 - Role: Accès aux Données(Repository), Mapping(Mapper), Model Metier(Models)

### Presentation : 
-  Description: fournit l'interface utilisateur, affichant les données aux utilisateurs finaux et rassemblant leurs entrées.
-  Role: Gestion des entrées (Middleware), Validation des entrées(DTO), Transformation(Adapter), Réponse

### Arborescence :


```
├───Application
│   ├───Contracts
│   │       IBookRepository.ts
│   │       ILoanRepository.ts
│   │       IPenaltyRepository.ts
│   │       IUserRepository.ts
│   │
│   └───Services
│           BookService.ts
│           LoanService.ts
│           PenaltyService.ts
│           UserService.ts
│
├───Domaine
│       Book.ts
│       Loan.ts
│       Penalty.ts
│       User.ts
│
├───Persistance
│   ├───Mappers
│   │       BookMapper.ts
│   │       LoanMapper.ts
│   │       PenaltyMapper.ts
│   │       UserMapper.ts
│   │
│   ├───Models
│   │       BookModel.ts
│   │       LoanModel.ts
│   │       PenaltyModel.ts
│   │       UserModel.ts
│   │
│   └───Repositories
│           BookRepository.ts
│           LoanRepository.ts
│           PenaltyRepository.ts
│           UserRepository.ts
│
└───Presentation
    ├───Adapters
    │       BookAdapter.ts
    │       LoanAdapter.ts
    │       PenaltyAdapter.ts
    │       StatsAdapter.ts
    │       UserAdapter.ts
    │
    ├───Controllers
    │       BookController.http
    │       LoanController.http
    │       PenaltyController.http
    │       UserController.http
    │
    ├───Dtos
    │   │   BookDto.ts
    │   │   LoanDto.ts
    │   │   PenaltyDto.ts
    │   │   UserDto.ts
    │   │
    │   └───Stats
    │           BookStatsDto.ts
    │           LoanStatsDto.ts
    │
    └───Middlewares
            AuthenticationMiddleware.ts 
```

## Flux des Données - Exemple : Affichage des Livres

### Etapes du Flux

Fonctionnalité : **"Afficher la liste des livres"**.

### Requête HTTP :

> Un client envoie une requête `GET /books` au **BookController**.


### Controller :

> `BookController` appelle `BookService` pour récupérer les livres.

### Repository :

> `BookRepository` récupère les données via une requête à la base de données ou API.

### Mapper :

> `BookMapper` transforme les données **persistées** (`BookModel`) en entités **Domaine** (`Book`).

### Retour des Données:

> Les entités sont converties en **DTOs** via `BookAdapter` pour être renvoyées au client.

## Schéma simplifié 

```
Client → BookController → BookService → IBookRepository → BookRepository → BookAdapter  → BookMapper
```

#  Règles métier implémentées

Voici les règles métier implémentées :
    
    - Un livre ne peut pas être emprunté s’il est déjà emprunté.
    - Les pénalités sont calculées sur la base d’un retard quotidien (ex. 1€ par jour de retard).
    - Chaque utilisateur peut emprunter jusqu’à 3 livres en même temps.
