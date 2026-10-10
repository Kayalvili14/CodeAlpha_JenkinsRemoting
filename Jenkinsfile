pipeline {
    agent { label 'docker-agent' }

    stages {
        stage('Download Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Kayalvili14/CodeAlpha_JavaGradle.git'
            }
        }

        stage('Build and Test') {
            steps {
                sh 'bash ./gradlew clean build'
            }
        }

        stage('Save Results') {
            steps {
                junit 'build/test-results/test/*.xml'
                archiveArtifacts artifacts: 'build/distributions/*.zip',
                                 fingerprint: true
            }
        }
    }
}