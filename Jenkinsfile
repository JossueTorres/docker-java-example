pipeline {
  agent any

    parameters {
        string(name:'TAG_NAME',defaultValue: '',description:'')
    }

    environment {
        DOCKER_CREDENTIAL_ID = 'PersonalDockerAccount'
        GITHUB_CREDENTIAL_ID = 'PersonalGithubAccount'
        KUBECONFIG_CREDENTIAL_ID = 'demo-kubeconfig'
        REGISTRY = 'docker.io'
        DOCKERHUB_NAMESPACE = 'oscartorres97'
        GITHUB_ACCOUNT = 'JossueTorres'
        APP_NAME = 'docker-java-example'
    }

    stages {
        stage ('Checkout scm') {
            steps {
                checkout(scm)
            }
        }

        // stage ('unit test') {
        //     steps {
        //         withMaven() {
        //             sh 'mvn clean  -gs `pwd`/docker-spring-boot/pom.xml test'
        //         }
        //     }
        // }
 
        stage ('Build & push') {
            steps {
                withMaven() {
                    sh 'mvn  -Dmaven.test.skip=true -gs `pwd`/docker-spring-boot/pom.xml clean package'
                    sh 'docker build -f Dockerfile-online -t $REGISTRY/$DOCKERHUB_NAMESPACE/$APP_NAME:SNAPSHOT-$BRANCH_NAME-$BUILD_NUMBER .'
                    withCredentials([usernamePassword(passwordVariable : 'DOCKER_PASSWORD' ,usernameVariable : 'DOCKER_USERNAME' ,credentialsId : "$DOCKER_CREDENTIAL_ID" ,)]) {
                        sh 'echo "$DOCKER_PASSWORD" | docker login $REGISTRY -u "$DOCKER_USERNAME" --password-stdin'
                        sh 'docker push  $REGISTRY/$DOCKERHUB_NAMESPACE/$APP_NAME:SNAPSHOT-$BRANCH_NAME-$BUILD_NUMBER'
                    }
                }
            }
        }

        // stage('push latest'){
        //    when{
        //      branch 'master'
        //    }
        //    steps{
        //         container ('maven') {
        //           sh 'docker tag  $REGISTRY/$DOCKERHUB_NAMESPACE/$APP_NAME:SNAPSHOT-$BRANCH_NAME-$BUILD_NUMBER $REGISTRY/$DOCKERHUB_NAMESPACE/$APP_NAME:latest '
        //           sh 'docker push  $REGISTRY/$DOCKERHUB_NAMESPACE/$APP_NAME:latest '
        //         }
        //    }
        // }

        // stage('deploy to dev') {
        //   when{
        //     branch 'master'
        //   }
        //   steps {
        //     input(id: 'deploy-to-dev', message: 'deploy to dev?')
        //     kubernetesDeploy(configs: 'deploy/dev-ol/**', enableConfigSubstitution: true, kubeconfigId: "$KUBECONFIG_CREDENTIAL_ID")
        //   }
        // }

        // stage('push with tag'){
        //   when{
        //     expression{
        //       return params.TAG_NAME =~ /v.*/
        //     }
        //   }
        //   steps {
        //       container ('maven') {
        //         input(id: 'release-image-with-tag', message: 'release image with tag?')
        //           withCredentials([usernamePassword(credentialsId: "$GITHUB_CREDENTIAL_ID", passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
        //             sh 'git config --global user.email "kubesphere@yunify.com" '
        //             sh 'git config --global user.name "kubesphere" '
        //             sh 'git tag -a $TAG_NAME -m "$TAG_NAME" '
        //             sh 'git push http://$GIT_USERNAME:$GIT_PASSWORD@github.com/$GITHUB_ACCOUNT/devops-java-sample.git --tags --ipv4'
        //           }
        //         sh 'docker tag  $REGISTRY/$DOCKERHUB_NAMESPACE/$APP_NAME:SNAPSHOT-$BRANCH_NAME-$BUILD_NUMBER $REGISTRY/$DOCKERHUB_NAMESPACE/$APP_NAME:$TAG_NAME '
        //         sh 'docker push  $REGISTRY/$DOCKERHUB_NAMESPACE/$APP_NAME:$TAG_NAME '
        //   }
        //   }
        // }

        // stage('deploy to production') {
        //   when{
        //     expression{
        //       return params.TAG_NAME =~ /v.*/
        //     }
        //   }
        //   steps {
        //     input(id: 'deploy-to-production', message: 'deploy to production?')
        //     kubernetesDeploy(configs: 'deploy/prod-ol/**', enableConfigSubstitution: true, kubeconfigId: "$KUBECONFIG_CREDENTIAL_ID")
        //   }
        // }
    }
}