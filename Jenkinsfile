pipeline{
    agent any
    stages{
        stage('checkout the code from github'){
            steps{
                 git url: 'https://github.com/shyam21998/pro3/'
                 echo 'github url checkout'
            }
        }
        stage('codecompile with sam'){
            steps{
                echo 'starting compiling'
                sh 'mvn compile'
            }
        }
        stage('codetesting with sam'){
            steps{
                sh 'mvn test'
            }
        }
        stage('qa with sam'){
            steps{
                sh 'mvn checkstyle:checkstyle'
            }
        }
        stage('package with sam'){
            steps{
                sh 'mvn package'
            }
        }
        stage('run dockerfile'){
          steps{
               sh 'docker build -t myimg1 .'
           }
         }
        stage('port expose'){
            steps{
                sh 'docker run -dt -p 8082:8082 --name c001 myimg1'
            }
        }   
    }
}
