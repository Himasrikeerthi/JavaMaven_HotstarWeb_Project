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
        
        stage('Artifact in Nexus') {
            steps {
                withMaven(
                    globalMavenSettingsConfig: 'settings.xml',
                    jdk: 'jdk21',
                    maven: 'maven3',
                    traceability: true
                ) {
                    sh 'mvn clean deploy -s /var/lib/jenkins/.m2/settings.xml'
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
                    credentialsId: 'Hima_Docker_Hub',
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
