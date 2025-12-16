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
                    url: 'https://github.com/ANAGHAMOHANAN/MavenApp-deployment-to-Ec2-with-JenkinsPipeline.git'
            }
        }

        stage('Build') {
            steps {
                bat 'mvn clean package'
            }
        }

        stage('Deploy to EC2') {
            steps {
                withCredentials([sshUserPrivateKey(
                    credentialsId: 'ec2-ssh-key',
                    keyFileVariable: 'SSH_KEY',
                    usernameVariable: 'SSH_USER'
                )]) {

                    bat """
                    echo Copying artifact to EC2...

                    "C:\\Program Files\\Git\\usr\\bin\\scp.exe" -o StrictHostKeyChecking=no -i "%SSH_KEY%" ^
                    target\\demo-1.0.0.jar ^
                    %SSH_USER%@54.237.154.55:/opt/app/

                    echo Starting application...

                    "C:\\Program Files\\Git\\usr\\bin\\ssh.exe" -o StrictHostKeyChecking=no -i "%SSH_KEY%" ^
                    %SSH_USER%@54.237.154.55 ^
                    "pkill -f demo-1.0.0.jar || true && nohup java -jar /opt/app/demo-1.0.0.jar > /opt/app/app.log 2>&1 &"
                    """
                }
            }
        }
    }

    post {
        success {
            echo "✅ Deployment completed successfully."
        }
        failure {
            echo "❌ Deployment failed."
        }
    }
}
