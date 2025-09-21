node {
    
    def mavenHome
    def mavenCMD
    def dockerCMD
    def tagName
    
    stage('Prepare environment') {
        echo 'Initialize all the variables'
        mavenHome = tool name: 'maven', type: 'maven'
        mavenCMD = "${mavenHome}/bin/mvn"
        dockerCMD = "docker"   // just call docker directly
        tagName = "3.0"
    }
    
    stage('Git Code Checkout') {
        try {
            echo 'Checkout the code from Git repository'
            git 'https://github.com/shyam21998/pro3'
        } catch (Exception e) {
            echo 'Exception occurred in Git Code Checkout Stage'
            currentBuild.result = "FAILURE"
            emailext(
                body: """Dear All,
                The Jenkins job ${JOB_NAME} has failed. Please check it immediately:
                ${BUILD_URL}""",
                subject: "Job ${JOB_NAME} #${BUILD_NUMBER} Failed",
                to: 'shubham@gmail.com'
            )
        }
    }
    
    stage('Build the Application') {
        echo "Cleaning... Compiling... Testing... Packaging..."
        sh "${mavenCMD} clean package"
    }
    
    stage('Publish Test Results') {
        junit 'target/surefire-reports/*.xml'
    }
    
    stage('Containerize the application') {
        echo "Creating Docker image"
        sh "docker build -t shubhamkushwah123/insure-me:${tagName} ."
    }
    
    stage('Push to DockerHub') {
        echo 'Pushing the docker image to DockerHub'
        withCredentials([string(credentialsId: 'dock-password', variable: 'dockerHubPassword')]) {
            sh "${dockerCMD} login -u shubhamkushwah123 -p ${dockerHubPassword}"
            sh "${dockerCMD} push shubhamkushwah123/insure-me:${tagName}"
        }
    }
    
    stage('Configure and Deploy to Test Server') {
        ansiblePlaybook(
            become: true,
            credentialsId: 'ansible-key',
            disableHostKeyChecking: true,
            installation: 'ansible',
            inventory: '/etc/ansible/hosts',
            playbook: 'ansible-playbook.yml'
        )
    }
}





