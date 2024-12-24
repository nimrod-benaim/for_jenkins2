pipeline {
    agent any
	
    triggers {
        pollSCM('* * * * *')  // Poll SCM every minute
    }
    stages {
        stage('remove Repository') {
            steps {
                echo 'removing repository...'
                sh 'rm -rf for_jenkins2'
            }
        }
		stage('Stop and Remove Docker Containers') {
            steps {
                script {
                    echo 'Stopping and removing all Docker containers...'
                    // Stop all running containers
                    sh '''
                    docker ps -q | xargs -r docker stop
                    '''
                    // Remove all containers (including stopped ones)
                    sh '''
                    docker ps -aq | xargs -r docker rm
                    '''
                }
            }
        }
		stage('Remove Docker Images') {
            steps {
                script {
                    echo 'Removing all Docker images...'
                    // Remove all Docker images
                    sh '''
                    docker images -q | xargs -r docker rmi -f
                    '''
                }
            }
        }
        stage('Clone Repository') {
            steps {
                echo 'Cloning the repository...'
                sh 'git clone https://github.com/nimrod-benaim/for_jenkins2.git/'
            }
        }
		stage('Copy .env File') {
            steps {
                echo 'Copying .env file...'
                withCredentials([file(credentialsId: '.env', variable: 'ENV_FILE')]) {
                    sh '''
                    cp "$ENV_FILE" for_jenkins2/.env
                    '''
                }
            }
        }
        stage('Run Docker Compose') {
            steps {
                script {
                    echo 'Starting Docker Compose...'
                    sh 'ls'
                    sh '''
                    cd for_jenkins2
                    docker-compose down -v # Stop any running services
                    docker-compose up -d --build  # Start services in detached mode
                    
                    '''
                }
            }
        }
		stage('Push Docker Image') {
            steps {
                script {
                    echo 'Pushing Docker Image to Docker Hub...'
                    withCredentials([usernamePassword(credentialsId: 'user_password_dockerhub', 
                                                     usernameVariable: 'DOCKER_USER', 
                                                     passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh '''
                        echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push nimrod1/cat_gif_site:ver-1.0
                        '''
                    }
                }
            }
        }
        stage('test') {
            steps {
                echo 'testing...'
                sh'sleep 25'
                sh 'curl http://localhost:5000'
            }
        }
    }
   
}
