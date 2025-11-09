# INFO 910 TP - Introduction DevOps
Alexis LEONARDUZZI

---
## Description de l'application

**DepoCheck** est une application web qui scanne les dépendances d’un dépôt GitHub (projets Java/Maven) et indique si elles sont à jours.

L'application est composée de trois services conteneurisés :

- **Frontend (depocheck-web)** : Interface utilisateur développée en React/Next.js
  - Port : 8080 (interne au conteneur)
  - Exposé sur : 8081
  
- **Backend (depocheck-api)** : API REST développée avec Spring Boot
  - Port : 3000 (interne au conteneur)
  - Exposé sur : 8080
  
- **Base de données** : PostgreSQL
  - Port : 5432 (accessible uniquement en interne au cluster)

---
## Déploiement

### Prérequis
- Minikube installé
- kubectl installé

### Démarage minikube
```bash
# Démarrer le cluster Minikube
minikube start
```

### Déployer l'application
Il est important de déployer les différents services dans l'ordre indiqué
```bash
# Secrets et ConfigMaps
kubectl apply -f k8s/secrets/
kubectl apply -f k8s/configmaps/

# Base de données PostgreSQL
kubectl apply -f k8s/postgress/

# Attendre que PostgreSQL soit prêt

# Backend et Frontend
kubectl apply -f k8s/back/
kubectl apply -f k8s/front/

# Ingress
kubectl apply -f k8s/ingress/
```

### Accéder au services
Pour accéder aux services depuis `localhost`:
```bash
minikube tunnel
```
Une fois que `minikube tunnel` est actif :

- **Frontend** : http://localhost:8081
- **Backend API** : http://localhost:8080

Toutes les routes de l'api sont protéger, donc il n'y a pas d'interet à intéragire avec directement.

---
## Structure du projet
```
INFO910/
├── apps/
│   ├── back/        # Code source du backend
│   └── front/       # Code source du frontend
├── k8s/
│   ├── secrets/     # Secrets Kubernetes
│   ├── configmaps/  # ConfigMaps
│   ├── postgress/   # Manifestes PostgreSQL
│   ├── back/        # Manifestes Backend
│   ├── front/       # Manifestes Frontend
│   └── ingress/     # Ingress
├── compose.yml      # Docker Compose (développement)
└── README.md
```

## Utiliser l'application
1. Créer un compte (inscription depuis le frontend).  
   Remarque : un bug d'affichage peut indiquer une erreur lors de la création alors que le compte est bien créé.

2. Se connecter avec le compte créé.

![[images/DepocheckLogin.png]]

3. Créer un repository sur el dashboard

![[images/DepocheckDashBoard.png]]

4. Ajouter un repository via le formulaire :
   - Nom utilisateur GitHub
   - URL du repository
   - Token GitHub (pour accéder aux repositories privés)

Pour ajouter un repository, il faut remplire le formulaire qui s'affiche avec des informations tel que  sont nom utilisateru github, le lien vers le repository ainsi qu'un token github pour s'authentifier et avoir accès à des repository privés.
![[images/DepocheckCreateRepo.png]]

5. Sur la page du repository, le système scanne les dépendances et affiche l'état.
![[images/DepocheckRepoView.png]]