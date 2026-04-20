node {
    docker.image('maven:3.9.4-jdk-21').inside {
        stage('Checkout') {
            checkout scm
        }

        stage('Build') {
            echo 'Building with Maven'
            sh 'mvn -B -DskipTests=false clean package'
        }

        stage('Test') {
            echo 'Running tests'
            sh 'mvn test'
        }

        stage('Archive') {
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
        }

        stage('Deliver') {
            if (fileExists('jenkins/scripts/deliver.sh')) {
                sh './jenkins/scripts/deliver.sh'
            } else {
                echo 'No deliver script found; skipping deliver.'
            }
        }
    }
}
