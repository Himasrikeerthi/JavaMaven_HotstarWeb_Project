pipeline {
    agent any
tools{
    maven'maven3'
    jdk'jdk21'
}
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

    stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sq') {
                    sh 'mvn sonar:sonar'
                }
            }
        }
        
  stage('Deploy') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'nexus-creds',
                    usernameVariable: 'NEXUS_USER',
                    passwordVariable: 'NEXUS_PASS'
                )]) {

                    sh '''
                    mvn clean deploy \
                    -Dusername=$NEXUS_USER \
                    -Dpassword=$NEXUS_PASS
                    '''
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t himasrikeerthi/hotstar-app:latest .'
            }
        }

        stage('Docker Login & Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker_creds',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    sh '''
                    set -e
                    echo $PASS | docker login -u $USER --password-stdin
                    docker push himasrikeerthi/hotstar-app:latest
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    sh '''
                    set -e
                    export KUBECONFIG=$KUBECONFIG

                    echo "Checking cluster..."
                    kubectl get nodes

                    echo "Deploying app..."
                    kubectl apply -f hotstar_deployment.yml

                    echo "Pods status..."
                    kubectl get pods
                    '''
                }
            }
        }
    }
}
