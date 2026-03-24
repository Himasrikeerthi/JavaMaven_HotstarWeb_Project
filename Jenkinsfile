pipeline {
    agent any

    environment {
        EMAIL = 'sriramyaganni26@gmail.com'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'master',
                    credentialsId: 'git-creds',
                    url: 'https://github.com/SriramyaGanni/JavaMaven_HotstarWeb_Project.git'
            }
            post {
                success {
                    emailext to: "${EMAIL}",
                    subject: "Checkout SUCCESS",
                    body: "Code checkout completed successfully"
                }
                failure {
                    emailext to: "${EMAIL}",
                    subject: "Checkout FAILED",
                    body: "Code checkout failed"
                }
            }
        }

        stage('Build Maven') {
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    emailext to: "${EMAIL}",
                    subject: "Build SUCCESS",
                    body: "Maven build completed successfully"
                }
                failure {
                    emailext to: "${EMAIL}",
                    subject: "Build FAILED",
                    body: "Maven build failed"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t sriramyaganni/hotstar-webapp:latest .'
            }
            post {
                success {
                    emailext to: "${EMAIL}",
                    subject: "Docker Build SUCCESS",
                    body: "Docker image built successfully"
                }
                failure {
                    emailext to: "${EMAIL}",
                    subject: "Docker Build FAILED",
                    body: "Docker build failed"
                }
            }
        }

        stage('Docker Login & Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                    sh 'docker push sriramyaganni/hotstar-webapp:latest'
                }
            }
            post {
                success {
                    emailext to: "${EMAIL}",
                    subject: "Docker Push SUCCESS",
                    body: "Docker image pushed successfully"
                }
                failure {
                    emailext to: "${EMAIL}",
                    subject: "Docker Push FAILED",
                    body: "Docker push failed"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f hotstar_deployment.yml'
            }
            post {
                success {
                    emailext to: "${EMAIL}",
                    subject: "Deployment SUCCESS",
                    body: "Application deployed successfully to Kubernetes"
                }
                failure {
                    emailext to: "${EMAIL}",
                    subject: "Deployment FAILED",
                    body: "Kubernetes deployment failed"
                }
            }
        }
    }

    post {
        always {
            emailext to: "${EMAIL}",
            subject: "Pipeline Completed",
            body: """
Pipeline Finished

Job: ${env.JOB_NAME}
Build Number: ${env.BUILD_NUMBER}
Status: ${currentBuild.currentResult}
URL: ${env.BUILD_URL}
"""
        }
    }
}
