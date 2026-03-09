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
                        mvn clean deploy --settings $MAVEN_SETTINGS
                    '''
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['ec2-ssh']) {
                    sh '''
                        echo "Copying artifact to EC2..."
                        scp -o StrictHostKeyChecking=no target/*.jar ec2-user@$EC2_IP:/home/ec2-user/app.jar

                        echo "Starting application on EC2..."
                        ssh -o StrictHostKeyChecking=no ec2-user@$EC2_IP '
                            pkill -f app.jar || true
                            nohup java -jar /home/ec2-user/app.jar > app.log 2>&1 &
                        '
                    '''
                }
            }
        }

    }
}
