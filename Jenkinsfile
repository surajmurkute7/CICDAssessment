def COLOR_MAP = [
    'SUCCESS': 'good',
    'FAILURE': 'danger'
    ]
pipeline {
    agent any
     environment {
        SCANNER_HOME = tool 'sonarqube'
    }
    stages {
        stage('git checkout') {
            steps {
             git 'https://github.com/surajmurkute7/CICDAssessment.git'
            }
        }
         stage('compile') {
            steps {
              sh 'mvn compile'
            }
        }
         stage('code analysis') {
            steps {
              withSonarQubeEnv('sonarqube-server') {
               sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Java-Springboot \
               -Dsonar.java.binaries=. \
               -Dsonar.projectKey=Java-Springboot'''
              }
            }
        }
        stage('package') {
            steps {
              sh 'mvn install'
            }
        }
         stage('docker build') {
            steps {
             script {
                 withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                    sh 'docker build -t assessmentcicd .'
                  }
              }
            }
        }
         stage('docker push') {
            steps {
             script {
                withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                    sh 'docker tag spring-boot surajmurkute/assessmentcicd:1.0'
                    sh 'docker push surajmurkute/assessmentcicd:1.0'
                  }
              }
            }
         }    
        stage('docker compose') {
            steps {
             script {
                 withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                    sh 'docker-compose up -d'
                  }
              }
            }
        }

    }
 
    post {
        always {
            echo 'slack Notification.'
            slackSend channel: '#cicdpipe',
            color: COLOR_MAP [currentBuild.currentResult],
            message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URl}"
            
        }
    }
}
