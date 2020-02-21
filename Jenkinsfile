pipeline {
    agent any
    
    parameters { 
         string(name: 'tomcat_dev', defaultValue: '18.217.163.36', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '18.217.163.36', description: 'Production Server')
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
                        sh "scp -i ~/Downloads/pub.pem **/target/*.war ubuntu@${params.tomcat_dev}:/home/ubuntu/apache-tomcat-staging/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "scp -i ~/Downloads/pub.pem **/target/*.war ubuntu@${params.tomcat_prod}:/home/ubuntu/apache-tomcat-prod/webapps"
                    }
                }
            }
        }
    }
}