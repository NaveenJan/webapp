pipeline {
  agent any 
  tools {
    maven 'Maven'
  }
  stages {
    stage ('Initialize') {
      steps {
        sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
            ''' 
            }
    }
    stage ('Check-Git-Secrets') {
      steps {
        sh 'rm trufflehog || true'
        sh 'docker run gesellix/trufflehog --json https://github.com/NaveenJan/webapp.git > trufflehog'
        sh 'cat trufflehog'
      }
    }
    stage ('Build') {
      steps {
      sh 'mvn clean package'
       }
    }    
       stage ('Deploy-To-Tomcat') 
    {
            steps {
           sshagent(['tomcat']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@18.116.49.84:/home/ubuntu/prod/apache-tomcat-8.5.69/webapps/webapp.war'
              }      
           }     
      stage ('DAST') {
      steps {
        sshagent(['DAST']) {
         sh 'ssh -o  StrictHostKeyChecking=no ubuntu@13.58.246.28 "docker run -t owasp/zap2docker-stable zap-baseline.py -t http://18.116.49.84:8080/webapp/" || true'
        }
      }
     }
  }
}
