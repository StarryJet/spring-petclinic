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
                // Gunakan 'del /f /s /q' untuk hapus file di Windows
                bat 'if exist apache-jmeter-5.6.3 del /f /s /q apache-jmeter-5.6.3'
                bat 'call mvnw.cmd clean package -DskipTests'
            }
        }

        stage('3. Run JMeter Performance Test') {
            steps {
                echo "Downloading and Running JMeter on Windows..."
                // Menggunakan PowerShell/Batch untuk download dan eksekusi
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
                echo "Pushing image to Docker Hub..."
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    // Pake format login Windows yang aman
                    bat "echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin"
                    bat "docker push ${DOCKER_IMAGE}"
                }
            }
        }
    }
}