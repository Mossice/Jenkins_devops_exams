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
