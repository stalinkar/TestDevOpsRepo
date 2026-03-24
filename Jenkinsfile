pipeline {
    agent { label 'docker' }
    triggers {
        pollSCM('H/2 * * * *')   // every 2 minutes
    }

    stages {
        stage('Clone Repository') {
            steps {
                    git branch: 'main',
                    url: 'git@github.com:stalinkar/TestDevOpsRepo.git',
                    credentialsId: 'github-creds'
            }
        }

        stage('Check the docker cli') {
            steps {
                sh "docker --version"
                sh "docker ps -a"
                sh "docker compose --version"
            }
        }
        stage('Docker build') {
            steps {
                echo "Docker build running"
                sh "docker build . -t pythonapp:v1"
            }
        }

        stage('Docker Container Cerateion') {
            steps {
                echo "Running docker compose here..."
                sh "docker compose up -d"
            }
        }
        stage('HTML Reporting'){
            steps {
                publishHTML (target : [allowMissing: false,
                 alwaysLinkToLastBuild: true,
                 keepAll: true,
                 reportDir: 'reports',
                 reportFiles: 'myreport.html',
                 reportName: 'My Reports',
                 reportTitles: 'The Report'])
            }
        }
    }
}
