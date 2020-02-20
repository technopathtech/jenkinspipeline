pipeline {
    agent any
    
    parameters { 
         string(name: 'tomcat_dev', defaultValue: '18.190.24.221', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '18.190.24.221', description: 'Production Server')
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
                        sh "scp -i /home/jenkins/pub.pem **/target/*.war ubuntu@${params.tomcat_dev}:/opt/tomcat/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "scp -i /home/jenkins/pub.pem **/target/*.war ubuntu@${params.tomcat_prod}:/opt/tomcat1/webapps"
                    }
                }
            }
        }
    }
}