pipeline {
    agent any
    environment {
        SWARM_SERVICE_NAME = 'stack_health'
    }
    stages {
        stage('Build with unit testing') {
            steps {
                sh """
                docker build -t ${env.SWARM_SERVICE_NAME}:${env.GIT_COMMIT} .
                """
            }
        }  

        stage('Update TEST swarm') {
            steps {
                sh """
                docker service update \
                --replicas 1 \
                --update-delay 10s \
                --env BUILD_NUMBER=${env.BUILD_NUMBER} \
                --env EXECUTE_SPACE=test \
                --image ${env.SWARM_SERVICE_NAME}:${env.GIT_COMMIT} \
                test_${env.SWARM_SERVICE_NAME}
                """
            }
        }    

        stage('Update PROD swarm') {
            steps {
                sh """
                docker service update \
                --replicas 1 \
                --update-delay 10s \
                --env BUILD_NUMBER=${env.BUILD_NUMBER} \
                --env EXECUTE_SPACE=test \
                --image ${env.SWARM_SERVICE_NAME}:${env.GIT_COMMIT} \
                prod_${env.SWARM_SERVICE_NAME}
                """
            }
        }    
    }
}