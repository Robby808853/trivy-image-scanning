
pipeline {
    agent any

    tools {
        maven "M3"
    }

    stages {
        stage('Build') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'ae4cfbb2-2987-463e-9b4d-04d585414cbc', url: 'https://github.com/AkashChhetri12/HelloWorldMaven.git']]])

                sh "mvn clean package"

            }

            post {
                success {
                    archiveArtifacts 'target/*.war'
                }
            }
            
        }
        
        stage('Build Docker Image'){
            steps{
              sh 'docker build -t akashchhetri/helloworlddeploy:${BUILD_ID} .'
            }
        }
        
        stage('Docker Repository'){
            steps{
              withCredentials([usernamePassword(credentialsId: '780592ac-035b-4fc9-88df-253d3045a082', passwordVariable: 'docker_pass', usernameVariable: 'docker_user')]) {
        	      sh 'docker login -u ${docker_user} -p ${docker_pass}'
              	  sh 'docker push akashchhetri/helloworlddeploy:${BUILD_ID}'
        	  }
            }
         }
         
        stage('Deploy Application'){
            steps{
              sh 'ansible-playbook -vvv -e "version_tag=${BUILD_ID}" ./ansible_docker_play.yml'
            }
         }
    }
}

