---
title: "4th Piece: Preventing Docker logouts with concurrent Jenkins builds"
date: 2019-10-28T22:14:18-04:00
draft: false
---

We were running into an issue with our Docker image builds when running them concurrently on the same Jenkins node. In order to pull or push images from/to the repository, Jenkins first needs to authenticate. By default, Docker will store the credentials for the session in the Jenkins user's `$HOME/.docker` directory, in the `config.json` file. But this means any other builds on that node -- or potentially any user with access to the Jenkins `$HOME` -- can see and use those credentials themselves.

The issue with these credentials being stored insecurely can be mitigated using [credential helpers](https://github.com/docker/docker-credential-helpers), but that doesn't necessarily prevent other Jenkins jobs from using it. But I'm getting a bit sidetracked here.

The issue we had was with concurrent builds on the same node, and authentication being lost after the first job completes. Because we want to clean up after each build and logout Jenkins from the Docker repo, once the first job does so, then *the other jobs running at that time lose their authentication*. This is because they're all using the same configuration directory.

Thankfully, Docker will honor the [`DOCKER_CONFIG`](https://docs.docker.com/engine/reference/commandline/cli/) environment variable when it's invoked. This will use that directory for configuration file writing, which includes the session's credential store. If we use a unique one for each job -- a location we're sure other jobs won't share -- we can reliably login/logout from the Docker repository without affecting any jobs that may be running at the same time.

Here's a quick example showing what we did:

```
pipeline {

    agent { label 'docker-build' } // Only build on nodes labeled 'docker-build'
    options {
        timestamps ()
    }

    environment {
        DOCKER_REPO         = "docker-repo.mydomain.com"              // Our local Docker repo
        DOCKER_CONFIG       = "${WORKSPACE}/${BUILD_NUMBER}/.docker"  // Unique location for this job
    }

    stages {
        stage('prep environment') {  // Create the location for the DOCKER_CONFIG for this job
            steps {
                sh(
                    script: """
                        mkdir -p "${DOCKER_CONFIG}"
                    """
                )
            }
        }
        stage('pull latest base images') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'jenkins-docker-repo', usernameVariable: 'REPO_USER', passwordVariable: 'REPO_PASSWORD')]) {
                    sh(
                        script: """
                            docker login -u "${REPO_USER}" -p "${REPO_PASSWORD}" ${DOCKER_REPO}
                            docker pull ${DOCKER_REPO}/my_project/build-base:stable
                        """
                    )
                }
            }
        }
```
We do a lot of stuff for the build in there and after it, of course. Then the post processing and cleanup:
```
    post {
        success { /* -- SNIP -- */ }
        failure { /* -- SNIP -- */ }
        cleanup {  // Lots of builds means lots of images we don't need to keep, so routinely clean them up
            sh(
                script: """
                    docker system prune -a -f --filter "until=2h"
                    docker volume ls -qf dangling=true | xargs -r docker volume rm
                    docker logout ${DOCKER_REPO}
                """
            )
            cleanWs()
        }
    }
```
Hope you find this useful!

-KM
