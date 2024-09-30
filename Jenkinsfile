pipeline {
    agent any

    options {
        buildDiscarder(logRotator(numToKeepStr: '5', daysToKeepStr: '5'))
        timestamps()
    }

    environment {
        registry = 'ju2021/house-price-prediction-api'
        registryCredential = 'dockerhub'
        dockerImage = ''
    }

    stages {
        stage('Test') {
            steps {
                echo 'Testing model correctness..'
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install -r requirements.txt
                    pytest
                    deactivate
                '''
            }
        }
        stage('Build') {
            steps {
                script {
                    echo 'Building image for deployment..'
                    dockerImage = docker.build(registry + ":$BUILD_NUMBER")
                }
            }
        }
        stage('Push') {
            steps {
                script {
                    echo 'Pushing image to dockerhub..'
                    docker.withRegistry('https://index.docker.io/v1/', registryCredential) {
                        dockerImage.push()
                        dockerImage.push('latest')
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying models..'
                echo 'Running a script to trigger pull and start a docker container'
                // Add your deployment steps here
            }
        }
    }

    post {
        always {
            echo 'Cleaning up..'
            sh 'docker rmi $registry:$BUILD_NUMBER'
            sh 'docker rmi $registry:latest'
        }
    }
}
