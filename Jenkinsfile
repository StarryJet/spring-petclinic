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
                echo "Downloading and Running JMeter via curl..."
                sh '''
                    if [ ! -d "apache-jmeter-5.6.3" ]; then
                        echo "Downloading JMeter..."
                        curl -L -O https://archive.apache.org/dist/jmeter/binaries/apache-jmeter-5.6.3.tgz
                        tar -xzf apache-jmeter-5.6.3.tgz
                    fi
                    echo "Running JMeter test..."
                    # Mengeksekusi jmx yang tadi udah lu pindahkan ke dalam folder src/test/jmeter
                    ./apache-jmeter-5.6.3/bin/jmeter -n -t src/test/jmeter/petclinic_test.jmx -l results.jtl
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