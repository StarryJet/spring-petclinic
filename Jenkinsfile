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
                // Menggunakan tanda './mvnw' dengan perintah 'sh' karena Jenkins berjalan di container Linux
                sh './mvnw clean package -DskipTests'
            }
        }

        stage('3. Run JMeter Performance Test') {
            steps {
                // Menjalankan tes JMeter secara headless di dalam lingkungan Linux
                sh 'jmeter -n -t petclinic_test.jmx -l results.jtl'
            }
        }

        stage('4. Docker Build') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('5. Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh "echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin"
                    sh "docker push ${DOCKER_IMAGE}"
                }
            }
        }
    }
}