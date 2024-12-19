# ArchiLivre

4 couches Applicatives 

## Description des couches et leur rôle

### Presentation : 
-  Description: Contient l'ensemble des interfaces utilisateur ou, dans le cas d'une API, contient les controllers et représente une couche tampon entre les règles métier et l'extérieur.
-  Role: Gestion des entrées et autorisation (Middleware), Validation des entrées (DTO), Transformation (Adapter), Réponses, Points d'entrée (Controllers)...

### Application : 
 - Description: Contient l'ensemble des règles métier de l'application. Elle manipule les entités de la couche Domain et fait appel aux repositories afin de persister les données via la couche de Persistance.
 - Role: Interfaces du communication avec les couches inférieures (Contracts), Exécution des règles métier(Services)

### Domaine :  
 - Description : Contient l'ensemble des entités métier, value objects, agrégats... manipulés par la couche Application. Couche ayant le plus haut niveau d'abstraction dans l'app.
 - Role: Isole les entités métier du reste de l'application.

### Persistance : 
 - Description: Gère la persistance des données et la communication avec les services externes ; elle contient les détails d'implémentation de ceux-ci.
 - Role: Accès aux Données (Repository), Mapping (Mapper), Model Metier (Models), Appels à des API tierces (peut être sorti dans une couche "Infrastructure").

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

## Flux des Données - Exemple : Création d'un emprunt

### Etapes du Flux

Fonctionnalité : **"Création d'un emprunt"**.

### Requête HTTP :

> Un client envoie une requête `POST /loan` au **LoanController** avec les donnéees requises dans le corps de la requête.

### Presentation :

> `LoanController` mappe les données d'entrée en Loan via LoanAdapter et appelle `LoanService`.

### Application :

> `LoanService` récupère les données et applique des règles métier, comme vérifier si l'utilisateur n'emprunte pas déjà 5 livres.
> Le service construit un objet Loan de la couche Domain et l'envoie au `LoanRepository`.

### Persistance :

> `LoanRepository` mappe Loan et LoanModel puis persiste celui-ci en base de données.
> Le repository mappe LoanModel en Loan via `LoanMapper` et le retourne à `LoanService`.

### Application :

> `LoanService` retourne les données au controller.

### Presentation :

> `LoanController` mappe les données en DTO via LoanAdapter et retourne le Loan créé au client.

## Flux de données

![image](https://github.com/user-attachments/assets/6153d38c-529d-4dbc-a986-2e18155e9c80)


#  Règles Métier Implémentées

Voici quelques exemples de règles métier implémentées :

### Gestion des Livres :
    
    -   Un livre ne peut pas être supprimé s'il est actuellement emprunté.
    -   Un livre doit avoir un titre, un auteur, et un nombre d'exemplaires > 0.
    
### Gestion des Emprunts :
    
    -   Un utilisateur ne peut emprunter plus de 5 livres simultanément.
    -   La durée maximale d'un emprunt est de 14 jours.
### Gestion des Pénalités :
    
    -   Si un utilisateur rend un livre en retard, une pénalité est appliquée.
    -   Le montant de la pénalité est proportionnel au nombre de jours de retard.
### Authentification :
    
    -   Seuls les utilisateurs authentifiés peuvent effectuer des emprunts.
    -   Un middleware `AuthenticationMiddleware` vérifie les jetons d'accès.
