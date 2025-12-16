pipeline {
    agent any

    tools {
        maven 'maven-3.9'
        jdk 'jdk-17'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/bhuvan-raj/MavenApp-deployment-to-Ec2-with-JenkinsPipeline.git'
            }
        }

        stage('Build') {
            steps {
                // *** MODIFIED: Added -X flag for verbose debugging to find the compilation error. ***
                sh 'mvn clean package -X' 
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['ec2-ssh-key']) {
                    sh """
                    # Note: Replace <EC2_PUBLIC_IP> with your actual IP address before running.
                    scp -o StrictHostKeyChecking=no target/demo-1.0.0.jar ec2-user@<EC2_PUBLIC_IP>:/opt/app/
                    ssh -o StrictHostKeyChecking=no ec2-user@<EC2_PUBLIC_IP> '
                        pkill -f demo-1.0.0.jar || true
                        nohup java -jar /opt/app/demo-1.0.0.jar > app.log 2>&1 &
                    '
                    """
                }
            }
        }
    }
}
