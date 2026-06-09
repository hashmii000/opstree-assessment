pipeline {
agent any


environment {
    IMAGE_NAME = "saiif/java-app"
    IMAGE_TAG = "latest"
}

stages {

    stage('Checkout') {
        steps {
            git branch: 'main',
                url: 'https://github.com/hashmii000/opstree-assessment.git'
        }
    }

    stage('Build Application') {
        steps {
            sh 'mvn clean package -DskipTests'
        }
    }

    stage('Build Docker Image') {
        steps {
            sh '''
            docker build \
            -t ${IMAGE_NAME}:${IMAGE_TAG} .
            '''
        }
    }

    stage('Push Docker Image') {
        steps {
            withCredentials([
                usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )
            ]) {
                sh '''
                echo "$DOCKER_PASS" | docker login \
                -u "$DOCKER_USER" \
                --password-stdin

                docker push ${IMAGE_NAME}:${IMAGE_TAG}
                '''
            }
        }
    }

    stage('Deploy') {
        steps {
            sh '''
            echo "Deployment Stage Started"

            kubectl apply -f k8s/ || true

            kubectl rollout restart deployment/java-app || true

            kubectl get pods || true

            echo "Deployment Stage Completed"
            '''
        }
    }
}

post {
    success {
        echo 'CI/CD Pipeline Completed Successfully'
    }

    failure {
        echo 'Pipeline Failed'
    }

    always {
        sh 'docker image ls | head'
    }
}


}

