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
                    scp target/jenkins-demo-1.0.0.jar ec2-user@$EC2_IP:/home/ec2-user/

                    ssh ec2-user@$EC2_IP "
                    pkill -f jenkins-demo || true
                    java -jar /home/ec2-user/jenkins-demo-1.0.0.jar &
                    "
                    '''

                }

            }

        }

    }

}
