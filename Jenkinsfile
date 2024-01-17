pipeline{
    agent any
    tools{
        maven 'Maven3'
    }
    environment {
        REGISTRY_CREDENTIALS = 'ecr:us-east-1:aws-cred'
    }
    stages{
	    stage("Parallel stage"){
	parallel{
		stage("test_parallel1"){
		      steps{
			      echo "test parallel"
		      }}
		stage("test_parallel2"){
		      steps{
			      echo "test parallel"
		      }}}}
        stage("Cleanup Workspace"){
            steps{
                cleanWs()
            }

         }
        stage("Github Checkout"){
            steps{
               git branch: 'main' , credentialsId: 'git' , url: 'https://github.com/pc9733/web-jenkins-pr'
            }
         
        }
        stage("Build-application"){
            steps{
                sh "mvn clean package"
            }
        }
       //stage("Test-application"){
       // steps{
       // sh "mvn test"
       /// }
        // }
       //stage("SonarQube Analysis"){
       //    steps {
	   //        script {
	   //        withSonarQubeEnv(credentialsId: 'jenkins_sonar') { 
       //                 sh "mvn sonar:sonar"
	   //        }
	   //        }	
       //    }
       //}
       // stage("Quality Gate"){
       //    steps {
       //        script {
       //             waitForQualityGate abortPipeline: false, credentialsId: 'jenkins_sonar'
       //         }	
       //     }
       //
       // }
    stage('Build Docker Image') {
        steps{
            script {

                sh "docker build -t register-app:${BUILD_NUMBER} ."
                sh "docker images"
              }
              }
    }
    stage('Push Docker Image to ECR') {
    steps {
        script {
            withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId:'aws-cred']]) {
            sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 120695692422.dkr.ecr.us-east-1.amazonaws.com'
            

            // Tag the Docker image with the ECR repository URL
            sh "docker tag register-app:${BUILD_NUMBER} 120695692422.dkr.ecr.us-east-1.amazonaws.com/register-app:${BUILD_NUMBER}"

            // Push the Docker image to ECR
            sh "docker push 120695692422.dkr.ecr.us-east-1.amazonaws.com/register-app:${BUILD_NUMBER}"
            
            }
        }
    }
}
stage('Deploy to EKS') {
    steps {
        script {
            script {
            withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId:'aws-cred']]) {
            sh "kubectl get all"
            // sh "kubectl config use-context <EKS_cluster_context>"
            // sh "kubectl apply -f deployment.yml" 
        }
            }
    }
}
}

stage('Check Deployment') {
    steps {
        script {
            sh "kubectl get all"
            // sh "sudo kubectl get deployments"
            // sh "sudo kubectl get services"
        }
    }
}
        

       }
}
