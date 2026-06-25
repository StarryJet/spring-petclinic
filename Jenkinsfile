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
                // Hapus sisa folder JMeter dari run sebelumnya sebelum Maven nge-scan!
                sh 'rm -rf apache-jmeter-*' 
                sh './mvnw clean package -DskipTests'
            }
        }

        stage('3. Run JMeter Performance Test') {
            steps {
                echo "Downloading and Running JMeter directly via CDN..."
                sh '''
                    echo "Downloading JMeter pack from fast CDN..."
                    curl -L -s -O https://dlcdn.apache.org//jmeter/binaries/apache-jmeter-5.6.3.tgz
                    tar -xzf apache-jmeter-5.6.3.tgz
                    
                    echo "Executing test plan..."
                    ./apache-jmeter-5.6.3/bin/jmeter -n -t petclinic_test.jmx -l results.jtl
                    
                    echo "Cleaning up JMeter folder so Maven Checkstyle won't scan it next time..."
                    rm -rf apache-jmeter-*
                '''
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