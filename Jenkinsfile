pipeline {
    agent any

    tools {
        maven 'maven'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Irfaanpk/MavenApp-deployment-to-Ec2-with-JenkinsPipeline.git'
            }
        }

        stage('Build') {
            steps {
                bat 'mvn clean package'
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['ec2-ssh-key']) {
                    bat '''
                        echo Copying artifact to EC2...

                        scp -o StrictHostKeyChecking=no ^
                        target\\demo-1.0.0.jar ^
                        ubuntu@44.200.101.190:/opt/app/

                        echo Starting application on EC2...

                        ssh -o StrictHostKeyChecking=no ubuntu@44.200.101.190 ^
                        "pkill -f demo-1.0.0.jar || true && ^
                        nohup java -jar /opt/app/demo-1.0.0.jar > /opt/app/app.log 2>&1 &"

                        echo Deployment command executed successfully
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "Deployment completed successfully."
        }
        failure {
            echo "Deployment failed. Check Jenkins or EC2 logs."
        }
    }
}
