pipeline {

    agent any

    tools {
        maven 'maven'
    }

    environment {
        EC2_IP = "44.222.63.84"
    }

    stages {

        stage('Build & Upload Artifact') {
            steps {
                configFileProvider([configFile(fileId: 'maven-settings', variable: 'MAVEN_SETTINGS')]) {
                    sh '''
                        echo "Building Maven project and uploading to GitHub Packages..."
                        mvn clean deploy --settings $MAVEN_SETTINGS
                    '''
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['ec2-ssh']) {
                    sh '''
                        echo "Checking artifact in target directory..."
                        ls -l target

                        echo "Copying JAR to EC2..."
                        scp -o StrictHostKeyChecking=no target/*.jar ec2-user@$EC2_IP:/home/ec2-user/app.jar

                        echo "Starting application on EC2..."
                        ssh -o StrictHostKeyChecking=no ec2-user@$EC2_IP << 'EOF'
                            pkill -f app.jar || true
                            nohup java -jar /home/ec2-user/app.jar > /home/ec2-user/app.log 2>&1 &
                            exit 0
                        EOF
                    '''
                }
            }
        }

    }
}
