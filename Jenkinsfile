pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'noukh/devops-tp3:latest'
        KUBECONFIG = "/var/jenkins_home/.kube/config"
    }

    stages {
        // stage('Cloner le dépôt') {
        //     steps {
        //         git branch: 'master', url: 'https://github.com/nourhen369/devops-tp3.git'
        //     }
        // }

        stage('Construire l\'image Docker') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_IMAGE} ."
                }
            }
        }

        stage('Pousser l\'image Docker') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker push $DOCKER_IMAGE'
                }
            }
        }

        stage('Déployer sur Kubernetes') {
            steps {
                withEnv(["KUBECONFIG=${env.KUBECONFIG}"]) {
                    sh '''
                        echo "Déploiement sur Kubernetes..."
                        kubectl apply -f deployment.yaml
                        kubectl apply -f service.yaml
                    '''
                }
            }
        }
    }
}
