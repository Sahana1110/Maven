pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo "Building Maven project..."
                dir('hello-world-maven/hello-world') {
                    sh 'mvn clean package'
                }
            }
        }

        stage('Deploy') {
    steps {
        echo "Deploying WAR file to Tomcat server..."

        sshagent(credentials: ['tomcat-ec2-key']) {
            dir('hello-world-maven/hello-world') {
                sh 'scp -o StrictHostKeyChecking=no target/hello-world.war ec2-user@13.204.26.205:/opt/tomcat/webapps/'
            }
            sh 'ssh -o StrictHostKeyChecking=no ec2-user@13.204.26.205 "sudo systemctl restart tomcat"'
        }
    }
}

        stage('Test') {
            steps {
                echo "Testing deployed application..."

                // Wait for Tomcat to load WAR
                sh 'sleep 15'

                // Curl test URL to confirm deployment success
                sh '''
                    curl --fail http://13.204.26.205:8080/hello-world/index.jsp
                '''
            }
        }
    }

    post {
        success {
            mail to: 'sahanams031@gmail.com',
                 subject: 'SUCCESS: hello-world deployed',
                 body: '''\
Hello,

Your 'hello-world' app has been successfully deployed on Tomcat 

Access it here: http://13.204.26.205:8080/hello-world/index.jsp

Regards,
Jenkins
'''
        }

        failure {
            mail to: 'sahanams031@gmail.com',
                 subject: 'FAILURE: hello-world deployment',
                 body: '''\
Hello,

Deployment or test failed for 'hello-world' on Tomcat.

Please check Jenkins logs.

Regards,
Jenkins
'''
        }
    }
}
