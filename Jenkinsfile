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
    
        stage('Clone Repository') {
            steps {
                echo 'Cloning the repository...'
                sh 'git clone https://github.com/nimrod-benaim/for_jenkins2.git/'
	        	sh 'cp /home/vboxuser/Desktop/.env /var/lib/jenkins/workspace/pipeline1/for_jenkins2'
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
        stage('test') {
            steps {
                echo 'testing...'
                sh'sleep 25'
                sh 'curl http://localhost:5000'
            }
        }
    }
   
}
