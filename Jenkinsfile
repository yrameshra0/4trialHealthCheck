pipeline {
    agent any
    environment {
        SWARM_SERVICE_NAME = 'space_health_check'
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
                // sh """docker service rm test_${SWARM_SERVICE_NAME}"""

                sh """
                docker service create \
                --replicas 1 \
                --name test_${SWARM_SERVICE_NAME} \
                --hostname ${SWARM_SERVICE_NAME} \
                --update-delay 10s \
                --network test \
                --env BUILD_NUMBER=env.BUILD_NUMBER \
                --env EXECUTE_SPACE=test \
                ${env.SWARM_SERVICE_NAME}:${env.GIT_COMMIT}
                """
            }
        }    

        stage('Update PROD swarm') {
            steps {
                // sh """docker service rm prod_${SWARM_SERVICE_NAME}"""

                sh """
                docker service create \
                --replicas 1 \
                --name prod_${SWARM_SERVICE_NAME} \
                --hostname ${SWARM_SERVICE_NAME} \
                --update-delay 10s \
                --network prod \
                --env BUILD_NUMBER=env.BUILD_NUMBER \
                --env EXECUTE_SPACE=prod \
                ${env.SWARM_SERVICE_NAME}:${env.GIT_COMMIT}
                """
            }
        }    
    }
}