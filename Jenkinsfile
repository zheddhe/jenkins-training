pipeline {
    agent any
    environment {
        SCHOOL = "datascientest"
        NAME = "Anthony"
        DOCKER_ID = "zheddhe"
        DOCKER_IMAGE = "datascientestapi"
        DOCKER_TAG = "v.${BUILD_ID}.0" 
    }
    stages {
        stage("Standalone Test on Env Variables") {
            environment {
                NAME = "lewis" // overrides pipeline level NAME env variable
                BUILD_ID = "2" // overrides the default BUILD_ID
            }
            steps {
                echo "SCHOOL = ${env.SCHOOL}" // prints "SCHOOL = datascientest"
                echo "NAME = ${env.NAME}" // prints "NAME = lewis"
                echo "BUILD_ID = ${env.BUILD_ID}" // prints "BUILD_ID = 2"
                script {
                    env.SOMETHING = "1" // creates env.SOMETHING variable
                }
            }
        }
        stage("Standalone Test on Override Env Variables") {
            steps {
                script {
                    env.SCHOOL = "I LOVE DATASCIENTEST!" // it can't override env.SCHOOL declared at the pipeline (or stage) level
                    env.SOMETHING = "2" // it can override env variable created imperatively
                }
                echo "SCHOOL = ${env.SCHOOL}" // prints "SCHOOL = datascientest"
                echo "SOMETHING = ${env.SOMETHING}" // prints "SOMETHING = 2"
                withEnv(["SCHOOL=DEV UNIVERSITY"]) { // it can override any env variable
                    echo "SCHOOL = ${env.SCHOOL}" // prints "SCHOOL = DEV UNIVERSITY"
                }
                withEnv(["BUILD_ID=1"]) {
                    echo "BUILD_ID = ${env.BUILD_ID}" // prints "BUILD_ID = 1"
                }
            }
        }
        stage('Building') {
            steps {
                  sh 'pip install -r requirements.txt'
            }
        }
        stage('Testing') {
            steps {
                  sh 'python -m unittest'
            }
        }
        stage('Deploying') {
            steps{
                script {
                    sh '''
                    docker rm -f jenkins
                    docker build -t $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG .
                    docker run -d -p 8000:8000 --name jenkins $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG
                    '''
                }
            }
        }
        stage('User Acceptance') {
          steps{
            input {
              message "Proceed with push to main"
              ok "Yes"
            }    
          }
        }
        stage('Pushing and Merging'){
            parallel {
                stage('Pushing Image') {
                    environment {
                        DOCKERHUB_CREDENTIALS = credentials('DOCKER_HUB_PASS')
                    }
                    steps {
                        sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                        sh 'docker push $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG'
                    }
                }
                stage('Merging') {
                    steps {
                        echo 'Merging done'
                    }
                }
            }
        }
    }
    post {
        always {
            sh 'docker logout'
        }
    }
}