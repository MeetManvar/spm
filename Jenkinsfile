properties([pipelineTriggers([githubPush()])])
pipeline {
    agent any

    stages {
        stage('Checkout SCM') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/MeetManvar/spm.git']]])
            }
        }
        stage('Clone'){
            steps{
                
                sh "rm -rf spm" 

                sh "git clone https://github.com/MeetManvar/spm.git"

            }
        }
     
        stage('Build'){
            steps{

                sh "docker login -u AWS -p $(aws ecr-public get-login-password --region us-east-1) public.ecr.aws/i4y9b5h8"

                
                sh """ 
                    cd git_prac_exe/ &&

                    docker build -t public.ecr.aws/i4y9b5h8/spm:latest . """
                
            }
        }

        stage('Push'){
            steps{

                sh " docker push public.ecr.aws/i4y9b5h8/spm:latest "
                
            }
        }
        
        stage('New_task'){
              steps{
                  sh '''
                  ls -al
                  aws --version
                  aws ecs register-task-definition --cli-input-json file://./container-def-cli.json 
                  '''
              }
          }
          
        stage('Stop Existing Task') {
            steps {
               
                sh '''
            aws ecs stop-task --cluster "yes" --task $(aws ecs list-tasks --cluster "yes" --service "web-service1" --output text --query taskArns[0])
            '''
            }
        }

        stage('Update Service') {
            steps {
               
                sh '''
                  aws ecs update-service --cluster yes --service web-service1 --task-definition web-family --force-new-deployment
                  ''' 
            }
        }
    }
}
