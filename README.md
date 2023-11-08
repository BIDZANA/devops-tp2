# DevOps TP2 - Mise en place de pipelines CI/CD avec GitHub Actions

Ce projet, baptisé "devops-tp2", vise à mettre en place un pipeline de CI/CD (Intégration Continue / Déploiement Continu) en utilisant GitHub Actions. L'objectif est de créer un processus automatisé pour la construction, les tests et le déploiement d'une application logicielle, en garantissant la qualité du code à l'aide de SonarCloud.

## Configuration de GitHub Actions

### Étape 1 : Setup GitHub Actions

La première étape consiste à configurer GitHub Actions, un service en ligne qui permet de créer des pipelines pour tester une application. Dans le dossier racine du projet, nous avons créé un fichier `main.yml` pour définir les étapes du pipeline. Le fichier `main.yml` doit être placé dans le répertoire `.github/workflows`.

```yaml
name: CI devops 2023
on:
  #to begin, you want to launch this job in the main and develop
  push:
    branches:
      - master
  pull_request:
    branches:
      - master

jobs:
  test-backend:
    runs-on: ubuntu-22.04
    steps:
      #checkout your GitHub code using actions/checkout@v2.5.0
      - uses: actions/checkout@v2.5.0

      #do the same with another action (actions/setup-java@v3) that enables to set up jdk 17
      - name: Set up JDK 17
        uses: actions/setup-java@v3
        with:
          distribution: 'temurin'
          java-version: 17


      #finally, build your app with the latest command
      - name: Build and test with Maven
        working-directory: "backend-api"
        run: mvn clean verify
```

Nous avons configuré GitHub Actions pour déclencher le pipeline lors de "push" sur la branche principale (main). Le travail "test-backend" exécute des étapes pour construire l'application, exécuter des tests et générer un rapport de qualité avec SonarCloud.

### Étape 2 : Mise en place de la publication Docker

Pour la phase de déploiement, nous construisons des images Docker et les publions sur Docker Hub. Nous utilisons des actions Docker pour accomplir cela. Les informations d'identification Docker Hub sont stockées en tant que secrets dans GitHub Actions.

### Étape 3 : Configuration de la qualité avec SonarCloud

La qualité du code est vérifiée à l'aide de SonarCloud. Nous avons enregistré un compte gratuit sur SonarCloud et configuré notre pipeline GitHub Actions pour exécuter des analyses SonarCloud après les tests.

```yaml
run: mvn -B verify sonar:sonar -Dsonar.projectKey=BIDZANA_devops-tp2 -Dsonar.organization=marvin92 -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=${{ secrets.SONAR_TOKEN }} --file ./backend-api/pom.xml
```

Le rapport de qualité est disponible sur SonarCloud, permettant de détecter les problèmes potentiels dans le code.

## Configuration des Secrets

Pour des raisons de sécurité, nous avons stocké les informations sensibles, telles que les jetons SonarCloud et les informations d'identification Docker Hub, en tant que secrets dans GitHub Actions. Cela garantit que ces informations ne sont pas exposées publiquement.

## Qualité du Code

L'utilisation de SonarCloud permet d'effectuer une analyse statique du code source, d'identifier les vulnérabilités potentielles, de mesurer la complexité du code et de fournir des recommandations pour améliorer la qualité du code.

## Intégration de TestContainers

Le projet utilise TestContainers pour exécuter des tests d'intégration. TestContainers est une bibliothèque Java qui permet d'exécuter des conteneurs Docker pendant les tests d'intégration. Cela inclut l'utilisation d'un conteneur PostgreSQL pour vérifier l'insertion et la récupération de données dans la base de données.

## Qualité et Sécurité

L'ensemble du processus de CI/CD vise à garantir la qualité du code, à détecter les problèmes potentiels et à automatiser la livraison du logiciel. Les informations sensibles sont gérées de manière sécurisée à l'aide de secrets, et les rapports de qualité sont générés à l'aide de SonarCloud.

## Bonus : Séparation des Pipelines

Pour répondre aux besoins spécifiques, les travaux du pipeline ont été séparés en différentes étapes pour qu'ils respectent les règles suivantes :
- Le travail "test-backend" est exécuté sur les branches "main" et "develop".
- Le travail "build-and-push-docker-image" est exécuté uniquement sur la branche "main".
- Le travail "build-and-push-docker-image" est déclenché uniquement si le travail "test-backend" est réussi.

Cela permet de contrôler le flux de travail en fonction des branches et d'assurer que le déploiement n'a lieu que sur la branche principale.

## Conclusion

Ce projet a permis de mettre en place un pipeline complet de CI/CD à l'aide de GitHub Actions. Il garantit la qualité du code, la construction d'images Docker et le déploiement automatisé. Les informations sensibles sont stockées en toute sécurité en utilisant des secrets, et SonarCloud est utilisé pour surveiller la qualité du code. Ce processus contribue à la création de logiciels de haute qualité, fiables et sécurisés.
