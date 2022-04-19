properties([pipelineTriggers([githubPush()])])
pipeline {
    agent any

    stages {
        stage('Checkout SCM') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: 'main']],
                    userRemoteConfigs: [[
                        url: 'https://github.com/MeetManvar/spm.git',
                        credentialsId: '',
                    ]]
                ])
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

                sh "aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/i4y9b5h8"

                
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
        stage('Stop Existing Task') {
            steps {
               
                sh '''aws ecs stop-task --cluster "myapp-cluster" --task $(aws ecs list-tasks --cluster "myapp-cluster" --service "testapp-service" --output text --query taskArns[0]) '''
            }
        }

        stage('Update Service') {
            steps {
               
                sh ''' aws ecs update-service --cluster myapp-cluster --service testapp-service --task-definition testapp-task --force-new-deployment '''
            }
        }
    }
}
