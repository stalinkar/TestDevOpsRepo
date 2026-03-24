pipeline {
    agent { label 'docker' }
    triggers {
        pollSCM('H/2 * * * *')   // every 2 minutes
    }

    stages {
        stage('Clone Repository') {
            steps {
                git url: 'https://github.com/stalinkar/TestDevOpsRepo.git',
                    branch: 'main'
            }
        }

        stage('Build') {
            steps {
                echo "Build step here..."
            }
        }

        stage('Test') {
            steps {
                echo "Test step here..."
            }
        }
    }
}
