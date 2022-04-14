properties([pipelineTriggers([githubPush()])])
pipeline {
    agent any

    // environment {
    //     POM_VERSION = getVersion()
    //     JAR_NAME = getJarName()
    //     VAR = "var"
    //     AWS_ECR_REGION = 'us-east-1'
    //     AWS_ECS_SERVICE = 'testapp-service'
    //     AWS_ECS_TASK_DEFINITION = 'testapp-task'
    //     AWS_ECS_COMPATIBILITY = 'FARGATE'
    //     AWS_ECS_NETWORK_MODE = 'awsvpc'
    //     //AWS_ECS_CPU = '256'
    //     //AWS_ECS_MEMORY = '512'
    //     AWS_ECS_CLUSTER = 'myapp-cluster'
    //     AWS_ECS_TASK_DEFINITION_PATH = './ecs/container-definition-update-image.json'
    // }
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
        stage('clone'){
            steps{
                
                sh "rm -rf spm" 

                sh "git clone https://github.com/MeetManvar/spm.git"

            }
        }
     
        stage('build'){
            steps{

                sh "aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/i4y9b5h8"

                // script
                // {
                //     VAR = sh(script: 'cd git_prac_exe/ && git rev-parse HEAD', returnStdout: true).trim()
                // }
                sh """ 
                    cd git_prac_exe/ &&

                    docker build -t public.ecr.aws/i4y9b5h8/spm:latest .
                  
                    docker push public.ecr.aws/i4y9b5h8/spm:latest """
                
            }
        }

        stage('Deploy---in---ECS') {
            steps {
               
                sh '''aws ecs stop-task --cluster "myapp-cluster" --task $(aws ecs list-tasks --cluster "myapp-cluster" --service "testapp-service" --output text --query taskArns[0])

                    aws ecs update-service --cluster myapp-cluster --service testapp-service --task-definition testapp-task --force-new-deployment '''
            }
        }
    }
}
