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

        stage('Cheking output') {
            steps {
               sh "docker ps"
                sh "docker compose ps"
            }
        }

        stage('app working') {
            steps {
               sh "curl localhost:9010"
            }
        }
        // stage('Image push') {
        //     steps {
        //        sh "aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 640168426521.dkr.ecr.us-east-1.amazonaws.com"
        //        sh "docker tag pythonapp:v1 640168426521.dkr.ecr.us-east-1.amazonaws.com/pythonapp:v1"
        //         sh "docker push 640168426521.dkr.ecr.us-east-1.amazonaws.com/pythonapp:v1"
        //     }
        // }
        steps {
            sh 'mkdir -p /home/ec2-user/project/workspace/git-pp01/reports'  // Ensure the reports directory exists
            sh 'rm -rf /home/ec2-user/project/workspace/git-pp01/reports/*'   // Clean the reports folder before use
            
            // Generate or copy your HTML report here, for example:
            // sh 'generate_report_command_here' 
            
            sh 'ls -l /home/ec2-user/project/workspace/git-pp01/reports'  // Ensure the report file is there
            
            publishHTML (target: [
                allowMissing: false,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: '/home/ec2-user/project/workspace/git-pp01/reports',  // Use absolute path
                reportFiles: 'myreport.html',
                reportName: 'MyReports',
                reportTitles: 'The Report'
            ])
        }
    }
}
