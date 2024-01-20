pipeline {
    agent{
       label 'docker'
     }
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockeruser')
       
    }
    stages{
        stage('Docker Build') {
            steps {
                sh 'docker build -t siriusjyothi/book-store .'
              }
          }
        stage('Docker Login & Push') {
            steps {
		sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                sh 'docker push siriusjyothi/book-store'
              }
          }
        stage('Remove Docker Images') {
            steps {
                sh 'docker rmi -f siriusjyothi/book-store'
              }
          }
        stage('Docker Network') {
            steps {
                script {
                    def existingNetwork = sh(script: "docker network ls --filter name=booknetwork --format '{{.Name}}'", returnStdout: true).trim()
                    if (!existingNetwork.contains('booknetwork')) {
                        sh 'docker network create -d bridge booknetwork'
                    } else {
                        echo 'booknetwork already exists.'
                    }
                }
            }
        }
       stage('Creating Database') {
            steps {
                script {
                    def existingMysqlContainer = sh(script: "docker ps -aqf name=mysql", returnStdout: true).trim()
                    if (existingMysqlContainer) {
                        echo 'MySQL container already exists.'
                    } else {
                        sh 'docker run -dt --name mysql --network booknetwork -p 3306:3306 -e MYSQL_ROOT_PASSWORD=Qwerty@123 mysql'
                    }
                }
            }
        }
      
	

    }
}
