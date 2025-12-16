pipeline {
    agent any

    tools {
        maven 'mymaven'
        jdk 'myjdk'
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
                        ubuntu@54.237.154.55:/opt/app/

                    echo Stopping existing application...
                    ssh -o StrictHostKeyChecking=no ubuntu@54.237.154.55 ^
                        "pkill -f demo-1.0.0.jar || true"

                    echo Starting application...
                    ssh -o StrictHostKeyChecking=no ubuntu@54.237.154.55 ^
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
