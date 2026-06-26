pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'starryjet/spring-petclinic:latest'
    }

    stages {
        stage('1. Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('2. Build') {
            steps {
                // Utilize 'del /f /s /q' to remove files on Windows
                bat 'if exist apache-jmeter-5.6.3 del /f /s /q apache-jmeter-5.6.3'
                bat 'call mvnw.cmd clean package -DskipTests'
            }
        }

        stage('3. Run JMeter Performance Test') {
            steps {
                echo "Downloading and Running JMeter on Windows..."
                // Utilize PowerShell/Batch for download and execution
                bat '''
                    powershell -Command "Invoke-WebRequest -Uri 'https://dlcdn.apache.org//jmeter/binaries/apache-jmeter-5.6.3.zip' -OutFile 'jmeter.zip'"
                    powershell -Command "Expand-Archive -Path 'jmeter.zip' -DestinationPath '.'"
                    
                    echo "Executing test plan..."
                    call apache-jmeter-5.6.3\\bin\\jmeter.bat -n -t petclinic_test.jmx -l results.jtl
                '''
            }
        }

        stage('4. Docker Build') {
            steps {
                bat "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('5. Docker Push') {
    steps {
        script {
            docker.withRegistry('', 'docker-hub-credentials') {
                bat "docker push ${DOCKER_IMAGE}"
                    }
                }
            }
        }
    }
    post {
        success {
            script {
                echo "Pipeline Working, Sends update to Jira..."
                // Sending log of success to Jira ticket SCRUM-6
                jiraAddComment site: 'meltzout', idOrKey: 'SCRUM-6', comment: 'Build, JMeter Test, and Docker Push executed successfully in Jenkins Pipeline.'
            }
        }
        failure {
            script {
                // Sending log of failure to Jira ticket SCRUM-6
                jiraAddComment site: 'meltzout', idOrKey: 'SCRUM-6', comment: 'Jenkins Pipeline failed during execution. Please check the console output.'
            }
        }
}
}