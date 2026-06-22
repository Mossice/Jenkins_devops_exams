# DATASCIENTEST JENKINS EXAM
# python-microservice-fastapi
Learn to build your own microservice using Python and FastAPI

## How to run??
 - Make sure you have installed `docker` and `docker-compose`
 - Run `docker-compose up -d`
 - Head over to http://localhost:8080/api/v1/movies/docs for movie service docs 
   and http://localhost:8080/api/v1/casts/docs for cast service docs
## GIT USE
### Restaurer un fichier, en se basant sur un build correspondant :
   ```git checkout develop
      git checkout 95fd8afe6eeb7fe082ad12db1e7e8f3ab24512bb -- Jenkinsfile

      git status
      git add Jenkinsfile
      git commit -m "Restore working Jenkinsfile from successful build"
      git push origin develop
   ```
### Propager ce même Jenkinsfile sur les autres branches
 - qa:

    ```
      git checkout qa
      git merge develop
      git push origin qa
    ```

 - staging:
   ```
     git checkout staging
     git merge develop
     git push origin staging
   ```

 - master:
    ```
      git checkout master
      git checkout develop -- Jenkinsfile
      git add Jenkinsfile
      git commit -m "Synchronize Jenkinsfile from develop"
      git push origin master
     ```


### Maintenant que tout fonctionne, il faut faire un tag Git correspondant à cette version stable :
     ```
       git checkout master
       git tag -a v1.0.0 -m "First stable CI/CD release"
       git push origin v1.0.0
     ```

Ceci permettra de revenir facilement à la première version complètement opérationnelle du projet.

### ----------------------------------------------

Pour un projet DevOps de démonstration, tu as désormais :

✅ GitHub Multibranch
✅ Jenkins Pipeline as Code
✅ Docker Build
✅ DockerHub Push
✅ Docker Compose Tests
✅ Helm
✅ Kubernetes K3s
✅ Déploiement DEV automatique
✅ Déploiement QA automatique
✅ Déploiement STAGING automatique
✅ Déploiement PROD depuis master
✅ Secret File (kubeconfig)
✅ Secret Text (DockerHub)
✅ Déploiement de deux microservices (Movie + Cast) avec leurs bases PostgreSQL

La prochaine amélioration naturelle serait d'ajouter :

Tests Helm (helm lint)
Quality Gate (SonarQube)
ArgoCD (GitOps)
Ingress + DNS
Certificats TLS avec cert-manager

Mais à ce stade, ton projet CI/CD Kubernetes est déjà cohérent et présentable en entretien ou en démonstration technique.
