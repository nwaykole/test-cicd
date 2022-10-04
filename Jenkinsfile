pipeline {
 environment {
  AWS_ACCESS_KEY_ID= credentials('AWS_ACCESS_KEY_ID')
  AWS_SECRET_ACCESS_KEY= credentials('AWS_SECRET_ACCESS_KEY')
 
 }
 agent any
 stages {
 stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/nwaykole/pydeploy.git']]])    
                  }
                      }

 // Building Docker images
    stage('Building image') 
  {
      steps{
        script {
          
         sh 'docker build -t pyflaskrepo:latest .' 
               }
           }
   }
  
  //Creating ECR regitry terraform
  stage('TF Plan') {
       steps {
         script {
           //cd ecr-create
           sh 'cd ecr-create && terraform init'
          sh 'cd ecr-create && terraform plan'
         }
       }
     }

stage('TF Apply') {
      steps {
        script {
          sh 'cd ecr-create && terraform apply --auto-approve'
         
        }
      }
    }
 
  stage('Push Image to ECR') {
   steps {
    script{
  sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 713842441534.dkr.ecr.us-east-1.amazonaws.com'
  sh 'docker tag pyflaskrepo:latest 713842441534.dkr.ecr.us-east-1.amazonaws.com/pyflaskrepo:latest'
  sh 'docker push 713842441534.dkr.ecr.us-east-1.amazonaws.com/pyflaskrepo:latest'
    }
   }
  }
   //Creating AWS ECS task and deploying image -terraform

 stage('ECS TF Plan') {
       steps {
         script {
           //cd ecr-create
           sh 'cd ecs-deploy && terraform init'
          sh 'cd ecs-deploy && terraform plan'
         }
       }
     }

stage('ECS TF Apply') {
      steps {
        script {
          sh 'cd ecs-deploy && terraform apply --auto-approve'
         
        }
      }
    }
  }
}

