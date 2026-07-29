pipeline {

    agent any


    environment {

        AWS_REGION = "ap-south-2"

        AWS_ACCOUNT_ID = "995739391172"

        ECR_REPOSITORY = "react-vite-app"

        IMAGE_TAG = "${BUILD_NUMBER}"

        ECR_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPOSITORY}"

    }


    stages {


        stage('Checkout Source Code') {

            steps {

                git branch: 'main',
                    url: 'https://github.com/PreetiSanchitaSamal/react-vite-eks.git'

            }

        }



        stage('Install Dependencies') {

            steps {

                sh 'npm install'

            }

        }



        stage('Build React Application') {

            steps {

                sh 'npm run build'

            }

        }



        stage('Build Docker Image') {

            steps {

                sh """

                docker build -t ${ECR_URI}:${IMAGE_TAG} .

                """

            }

        }



        stage('Push Docker Image') {

            steps {

                sh """

                aws ecr get-login-password --region ${AWS_REGION} | \
                docker login --username AWS \
                --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com


                docker push ${ECR_URI}:${IMAGE_TAG}

                """

            }

        }



        stage('Update Kubernetes Deployment') {

            steps {

                sh """

                kubectl set image deployment/react-vite-app \
                react-vite-container=${ECR_URI}:${IMAGE_TAG}

                """

            }

        }



        stage('Verify Deployment Status') {

            steps {

                sh """

                kubectl rollout status deployment/react-vite-app


                kubectl get deployments


                kubectl get pods


                kubectl get service

                """

            }

        }

    }


    post {

        success {

            echo 'CI/CD Pipeline completed successfully!'

        }


        failure {

            echo 'Pipeline failed. Check the console output.'

        }

    }

}