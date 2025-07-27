pipeline{
    agent none
    stages{
        stage("Clone")
        {
            agent{
                label 'agent-1'
            }
            steps{
                git url:"https://github.com/dhruvjain-github/two-tier-flask-app.git"
            }
        }
        
        stage("Build and push to dockerHub")
        {
            agent{
                label 'agent-1'
            }
            steps{
                withCredentials([usernamePassword(credentialsId: 'DockerHub Credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')])
                {
                    sh '''
                    docker login -u "$DOCKER_USERNAME" -p "$DOCKER_PASSWORD"
                   
                    docker build -t $DOCKER_USERNAME/two-tier-flask-app:latest .
                    echo "$DOCKER_USERNAME"
                    echo "Build completed"
                    docker push $DOCKER_USERNAME/two-tier-flask-app:latest
                    
                    '''
                    
                }
            }
        }
        
        stage("Pull and build")
        {
            agent{
                label 'agent-2'
            }
            steps{
              withCredentials([usernamePassword(credentialsId: 'DockerHub Credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')])
                {
                    sh '''
                    docker login -u "$DOCKER_USERNAME" -p "$DOCKER_PASSWORD"
                    docker pull $DOCKER_USERNAME/two-tier-flask-app:latest
                    docker compose up -d
                    docker ps
                    '''
                    
                }
            }
        }
    }
}
