pipeline {
  agent any
  tools{
    maven 'localMavin'
  }
  parameters {
    string(name: 'tomcat_dev', defaultValue: '54.147.3.155', description: 'Staging Server')
    string(name: 'tomcat_prod', defaultValue: '54.175.65.122', description: 'Production Server')
  }
  triggers {
    pollSCM('* * * * *')
  }
  stages{
    stage('Build'){
      steps {
        bat "echo %cd%"
        bat 'mvn clean package'
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
            bat "pscp -i /Jenkins/tomcat-demo.ppk /Jenkins/maven-project/webapp/target/webapp.war ec2-user@${params.tomcat_dev}:/var/lib/tomcat7/webapps"
          }
        }
        stage ("Deploy to Production"){
          steps {
            bat "pscp -i /Jenkins/tomcat-demo.ppk /Jenkins/maven-project/webapp/target/webapp.war ec2-user@${params.tomcat_prod}:/var/lib/tomcat7/webapps"
          }
        }
      }
    }
  }
}
