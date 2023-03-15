pipeline{
    agent any

    tools {
        maven 'Maven_3.8.7'       
    }

    stages{
        stage('SCM Checkout'){
            steps{
                checkout scmGit(branches: [[name: '*/main']], extensions: [], gitTool: 'Default', userRemoteConfigs: [[credentialsId: 'Git_token', url: 'https://github.com/smrtviju/java-maven-war-app.git']])
            }
            
        }

        stage('maven-build'){
            steps{
                sh 'mvn clean install'
            }
        }

        stage('sonarqube'){
             steps{
                 withSonarQubeEnv("SonarQube") {
                         sh "${tool("Sonar 4.8")}/bin/sonar-scanner \
                         -Dsonar.projectKey=java-maven-app \
                         -Dsonar.java.binaries=target \
                         -Dsonar.host.url=http://172.31.21.62:9000 \
                         -Dsonar.login=sqp_ec570a382b3dde8332bdd86a728b1fb064ca63c8"
                     }
             }
         }

        stage('nexus-upload'){
            steps{
                sh 'mvn -s settings.xml clean deploy'
            }
        }

        stage("deployment"){
            agent{
                label 'Agent_node'
            }
            steps{
                sh 'ansible-playbook -i inventory deployment_playbook.yml -e "build_number=${BUILD_NUMBER}"'           
            }
        }
    }

}