pipeline{
 
   agent any

   stages{

     stage('checkout'){
        steps{
        
        echo 'source code checked from github'

        }

     }

     stage('verify tools'){

        steps
        {
            bat 'git --version'
            bat 'docker --version'
        }
     }

     stage('build docker image'){
        steps{
            bat 'docker build -t devops-platform1:%BUILD_NUMBER% .'
        }
     }
    
   }

}