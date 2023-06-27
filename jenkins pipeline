pipeline{
    agent any 
    environment {
        buildNumber= "BUILD_NUMBER"
    }
    stages{
        stage("Git Checkout"){
            steps{
                git "https://github.com/rajasekhars12/boxfuse-sample-java-war-hello.git"
            }
        }   
        stage("Maven Build"){
            steps{
                sh  "mvn clean package test install "
            }
        }
        stage("Rename_arfile"){
            steps{
                sh  "mv target/*.war target/myapp.war"
            }
        }
		        stage("sonar scan"){
            steps{
                sh "mvn sonar:sonar \
                      -Dsonar.projectKey=jenkins \
                      -Dsonar.host.url=http://54.166.139.12:9000 \
                      -Dsonar.login=386c565933a8b89f772eeb8a04dc8ce2638456d7"
            }
        }
        stage("Dokcer Build for pre-prod"){
            steps{ 
                sh "docker stop raja"
                sh "docker rm raja"
                sh "docker system prune"
                sh "docker build -t rajasekhardevops12/tomcat ."
                sh "docker run -itd --name raja -p 9080:8080 rajasekhardevops12/pre-prod/tomcat"
            }
        }
		stage("Dokcer Build prod"){
            steps{ 
                sh "docker stop raja"
                sh "docker rm raja"
                sh "docker system prune"
                sh "docker build -t rajasekhardevops12/tomcat ."
                sh "docker run -itd --name raja -p 9080:8080 rajasekhardevops12/pre-prod/tomcat"
            }
        }
		
         stage("Image pushing to docker hub"){
            steps{ 
                withCredentials([string(credentialsId: 'docker-passwd', variable: 'dockerhubpasswd')]) {
                   sh "docker login -u rajasekhardevops12 -p ${dockerhubpasswd}"
                }
                sh "docker push rajasekhardevops12/tomcat"
            }    
        }
        stage("Deploy_Production"){
            steps{
                sshagent(['Deploy_prod']){
                sh """
                    ssh -o StrictHostKeyChecking=no ubuntu@172.31.20.218 kubectl apply -f myapp.yml
                """    
            }
            }
        }       
    }
}
