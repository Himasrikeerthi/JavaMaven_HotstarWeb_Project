pipeline {
    agent any
    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'master',
                    credentialsId: 'github-creds',
                    url: 'https://github.com/Himasrikeerthi/JavaMaven_HotstarWeb_Project.git'
                }
        }

        stage('Build Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t himasrikeerthi/hotstar-app:latest .'
            }
        }

        stage('Docker Login & Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'Hima_Docker_Hub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                    sh 'docker push himasrikeerthi/hotstar-app:latest'
                }
            }
        }
        stage('Deploy to k8s') {
    steps {
        sh 'kubectl apply -f hotstar_deployment.yml'}
        }
    }
}
