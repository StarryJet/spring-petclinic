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
                echo "Running local JMeter from Windows Host via Maven Exec..."
                // Memanggil jmeter.bat yang ada di folder laptop lu langsung!
                // Sesuaikan 'C:/jmeter/bin/jmeter.bat' dengan lokasi tempat lu ekstrak JMeter di awal tadi
                sh 'mvn exec:exec -Dexec.executable="C:/jmeter/bin/jmeter.bat" -Dexec.args="-n -t src/test/jmeter/petclinic_test.jmx -l results.jtl"'
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