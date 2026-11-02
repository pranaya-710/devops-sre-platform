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

     stage('to test the application'){
        steps{
         
        script{

            def containerName = "devops-test-${env.BUILD_NUMBER}"

            try {

                bat "docker run -d --name ${containerName} devops-platform1:${env.BUILD_NUMBER}"

                bat 'timeout /t /3 /nobreak >nul'

                bat """

                docker exec ${containerName} python -c python -c "import urllib.request; r=urllib.request.urlopen('http://127.0.0.1:5000/health'); print(r.read().decode()); assert r.status == 200"
                        """
            }
            finally{

                 bat(
                            script: "docker rm -f ${containerName}",
                            returnStatus: true
                        )
            }
        } 
            
     
     }

    
   }
   
   }


}