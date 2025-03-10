pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'shrij34/online_shop:latest'  // Change this to your Docker Hub repo
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo 'Cloning Repository...'
                git branch: 'Hackathon', credentialsId: "${env.git-credentials}", url:'https://github.com/Shrij34/online_shop.git'
            }
        }

        stage('Build') {
            steps {
                echo 'Building Node.js application...'
                sh 'npm install'
                sh 'npm run build'
            }
        }

        stage('Test') {
            steps {
                echo 'Running Tests...'
                sh 'npm test'
            }
        }

        stage('Security Scan - Trivy') {
            steps {
                echo 'Running Trivy Scan...'
                sh 'trivy filesystem --exit-code 1 --severity HIGH,CRITICAL ./'
            }
        }

        stage('Security Scan - OWASP Dependency Check') {
            steps {
                echo 'Running OWASP Dependency Check...'
                sh 'dependency-check.sh --project online_shop --scan ./ --format ALL'
            }
        }

        stage('Docker Build & Push') {
            steps {
                echo 'Building Docker Image...'
                sh 'docker build -t $DOCKER_IMAGE .'
                echo 'Pushing Docker Image to Docker Hub...'
                withCredentials([usernamePassword(credentialsId: 'DOCKERHUB_CREDENTIALS', usernameVariable: 'DOCKERHUB_USERNAME', passwordVariable: 'DOCKERHUB_PASSWORD')]) {
                    sh 'echo $DOCKERHUB_PASSWORD | docker login -u $DOCKERHUB_USERNAME --password-stdin'
                    sh 'docker push $DOCKER_IMAGE'
                }
            }
        }

        stage('Deploy to KinD') {
            steps {
                echo 'Deploying to KinD Cluster...'
                sh 'kubectl apply -f kubernetes/deployment.yaml'
                sh 'kubectl apply -f kubernetes/service.yaml'
                sh 'kubectl apply -f kubernetes/ingress.yaml'
            }
        }
    }

    post {
        success {
            // In Manage Credentials => Global credentials 
            //  Kind: Secret text Secret: "Your mail id here " 
            // ID: RECIPIENT_EMAIL (you can choose any ID)

            echo 'Pipeline completed successfully!'
            mail to: "$RECIPIENT_EMAIL",
                 subject: "Jenkins Pipeline Success",
                 body: "The CI/CD pipeline has completed successfully."
        }
        failure {
            echo 'Pipeline failed. Check console output for details.'
            mail to: "$RECIPIENT_EMAIL",
                 subject: "Jenkins Pipeline Failure",
                 body: "The CI/CD pipeline has failed. Please check Jenkins for details."
        }
    }
}


