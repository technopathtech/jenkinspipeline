pipeline {
    agent any
    
    parameters { 
         string(name: 'tomcat_dev', defaultValue: 'ec2-18-217-163-36.us-east-2.compute.amazonaws.com', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: 'ec2-18-217-163-36.us-east-2.compute.amazonaws.com', description: 'Production Server')
    } 

    triggers {
         pollSCM('* * * * *') // Polling Source Control
     }

stages{
        stage('Build'){
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Staging'){
                    steps {
                        sh "scp -v -i ~/Downloads/pub.pem -o StrictHostKeyChecking=no **/target/*.war ubuntu@${params.tomcat_dev}:/home/ubuntu/apache-tomcat-staging/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "scp -v -i ~/Downloads/pub.pem -o StrictHostKeyChecking=no **/target/*.war ubuntu@${params.tomcat_prod}:/home/ubuntu/apache-tomcat-prod/webapps"
                    }
                }
            }
        }
    }
}