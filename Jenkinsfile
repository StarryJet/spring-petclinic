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

        stage('2. Maven Package') {
            steps {
                // Menggunakan maven wrapper bawaan Spring PetClinic buat nge-build .jar
                // Jika lu pakai Windows, ubah './mvnw' menjadi 'mvnw.cmd'
                bat 'mvnw.cmd clean package -DskipTests'
            }
        }

        stage('3. Run JMeter Performance Test') {
            steps {
                // Menjalankan JMeter headless pakai file .jmx yang lu bikin tadi
                bat 'jmeter -n -t petclinic_test.jmx -l results.jtl'
            }
        }

        stage('4. Docker Build') {
            steps {
                bat "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('5. Docker Push') {
            steps {
                // Memakai credentials yang dipasang di Jenkins tadi
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    bat "echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin"
                    bat "docker push ${DOCKER_IMAGE}"
                }
            }
        }
    }
}