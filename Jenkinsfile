node {
    docker.image('maven:3.9.14').inside {
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
        stage('Manual Approval') {
            echo 'Awaiting manual approval to proceed to Deploy'
            def userChoice = input message: 'Lanjutkan ke tahap Deploy?', parameters: [choice(name: 'ACTION', choices: 'Proceed\nAbort', description: 'Pilih opsi: Proceed untuk melanjutkan, Abort untuk membatalkan')]
            if (userChoice == 'Abort') {
                error 'Pipeline aborted by user at Manual Approval stage'
            }
        }

        stage('Deploy') {
            echo 'Deploying application'
            if (fileExists('jenkins/scripts/deliver.sh')) {
                sh './jenkins/scripts/deliver.sh'
            } else {
                echo 'No deliver script found; skipping deploy.'
            }

            echo 'Application deployed — keeping it running for 60 seconds before stopping automatically'
            sleep 60

            if (fileExists('jenkins/scripts/kill.sh')) {
                sh './jenkins/scripts/kill.sh'
            } else {
                echo 'No kill script found; skipping shutdown.'
            }
        }
    }
}
