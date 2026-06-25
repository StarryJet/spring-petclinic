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
                // Menggunakan './mvnw' dengan perintah 'sh' karena Jenkins berjalan di container Linux
                sh './mvnw clean package -DskipTests'
            }
        }

        stage('3. Run JMeter Performance Test') {
            steps {
                // Taktik otomatis: Download, ekstrak, dan jalankan JMeter langsung di dalam container
                sh '''
                    if [ ! -d "apache-jmeter-5.6.3" ]; then
                        echo "Downloading JMeter..."
                        wget https://archive.apache.org/dist/jmeter/binaries/apache-jmeter-5.6.3.tgz
                        tar -xzf apache-jmeter-5.6.3.tgz
                    fi
                    echo "Running JMeter test..."
                    ./apache-jmeter-5.6.3/bin/jmeter -n -t petclinic_test.jmx -l results.jtl
                '''
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