pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "mohamed/timesheet:1.0"
        KUBE_NAMESPACE = "chap4"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/nizarchaieb/timesheet-project.git'
            }
        }

        stage('Build Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t $DOCKER_IMAGE ."
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                    sh "docker push $DOCKER_IMAGE"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh "kubectl apply -f ~/chap4/timesheet-config.yml -n $KUBE_NAMESPACE"
                sh "kubectl apply -f ~/chap4/timesheet-secret.yml -n $KUBE_NAMESPACE"
                sh "kubectl apply -f ~/chap4/timesheet-deployment.yml -n $KUBE_NAMESPACE"
                sh "kubectl apply -f ~/chap4/timesheet-service.yml -n $KUBE_NAMESPACE"
            }
        }
    }
}
