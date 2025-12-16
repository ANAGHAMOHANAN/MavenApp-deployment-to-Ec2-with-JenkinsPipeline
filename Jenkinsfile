pipeline {
    agent any

    tools {
        maven 'maven-3.9'
        jdk 'JDK17'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/anaghamohanan/MavenApp-deployment-to-Ec2-with-JenkinsPipeline.git'
            }
        }

        stage('Build') {
            steps {
                bat 'mvn clean package'
            }
        }

        stage('Deploy to Ubuntu EC2') {
            steps {
                sshagent(['ec2-ssh-key']) {
                    bat '''
                    echo Copying artifact to EC2...
                    scp -o StrictHostKeyChecking=no ^
                        target\\demo-1.0.0.jar ^
                        ubuntu@34.224.84.252:/opt/app/

                    echo Stopping existing application...
                    ssh -o StrictHostKeyChecking=no ubuntu@34.224.84.252 ^
                        "pkill -f demo-1.0.0.jar || true"

                    echo Starting application...
                    ssh -o StrictHostKeyChecking=no ubuntu@34.224.84.252 ^
                        "nohup java -jar /opt/app/demo-1.0.0.jar > /opt/app/app.log 2>&1 &"

                    echo Deployment completed
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "✅ Deployment completed successfully."
        }
        failure {
            echo "❌ Deployment failed. Check Jenkins and EC2 logs."
        }
    }
}
