pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = 'lalbudha47/new-django-app:latest'
        CONTAINER_NAME = 'my-django-container'
    }
    
    stages {
        stage ('Fetch Code') {
            steps{
                echo 'Cloning code from github'
                git url:'https://github.com/codeboylal/Project-8-Django-new-todo.git', branch: 'main'
            }
        }
        
        stage ('Build') {
            steps {
                echo 'Building the image'
                sh 'docker build -t new-django-app .'
            }
        }
        
        // Alternative way of adding and using credentials

        // stage('Push to DockerHub') {
        //     steps {
        //         echo 'Pushing image to dockerhub'
        //         withCredentials([usernamePassword(credentialsId: 'DockerHub', usernameVariable: 'dockerhubUser', passwordVariable: 'dockerhubPass')]) {
        //             sh "docker login -u ${env.dockerhubUser} -p ${dockerhubPass}"
        //         }
        //     }
        // }

        stage ('Push to DockerHub') {
            steps {
                echo 'Pushing image to dockerhub'
                withCredentials([usernamePassword(credentialsId: 'docker-connection', usernameVariable: 'dockerhubUser', passwordVariable: 'dockerhubPass')]) {
                    sh "docker tag new-django-app $dockerhubUser/new-django-app:latest"
                    sh "docker login -u $dockerhubUser -p $dockerhubPass"
                    sh "docker push $dockerhubUser/new-django-app:latest"
                }
            }
        }


        // This is code block is recommended for deploying production-like environment - First it down and remove all containers and build the clean new one
        // The alternative is docker-compose but it's not working here so used insted. 
        stage('Deploy') {
            steps {
                echo 'Deploying to container'
                sh "docker pull ${DOCKER_IMAGE}"
                
                echo 'Stopping previous container'
                sh "docker stop ${CONTAINER_NAME} || true"
                
                echo 'Removing previous container'
                sh "docker rm ${CONTAINER_NAME} || true"
                
                echo 'Starting new container'
                sh "docker run -d --name ${CONTAINER_NAME} -p 8000:8000 ${DOCKER_IMAGE}"
            }
        }
    }
}
