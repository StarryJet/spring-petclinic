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
                sh './mvnw clean package -DskipTests'
            }
        }

        stage('3. Run JMeter Performance Test') {
            steps {
                echo "Running JMeter Performance Test via Maven Lifecycle..."
                // Menggunakan surefire.skip buat matiin unit test biasa, jadi cuma JMeter yang jalan!
                sh './mvnw verify -Dsurefire.skip=true'
            }
        }

        // stage('4. Docker Build') {
        //     steps {
        //         sh "docker build -t ${DOCKER_IMAGE} ."
        //     }
        // }

        // stage('5. Docker Push') {
        //     steps {
        //         withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
        //             sh "echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin"
        //             sh "docker push ${DOCKER_IMAGE}"
        //         }
        //     }
        // }
    }
}