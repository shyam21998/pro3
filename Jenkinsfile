node {

    def mavenHome
    def mavenCMD
    def dockerCMD
    def tagName

    stage('Prepare environment') {
        echo 'Initialize all the variables'
        mavenHome = tool name: 'maven', type: 'maven'
        mavenCMD = "${mavenHome}/bin/mvn"
        dockerCMD = "docker"
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
        echo "Building Docker image"
        sh "${dockerCMD} build -t shubhamkushwah123/insure-me:${tagName} ."
    }

    stage('Push to DockerHub') {
        echo 'Pushing Docker image to DockerHub'
        withCredentials([usernamePassword(
            credentialsId: 'dockerhub-credentials', // Your Jenkins DockerHub credentials ID
            usernameVariable: 'DOCKER_USER',
            passwordVariable: 'DOCKER_PASS'
        )]) {
            sh "${dockerCMD} login -u ${DOCKER_USER} -p ${DOCKER_PASS}"
            sh "${dockerCMD} push shubhamkushwah123/insure-me:${tagName}"
        }
    }

    stage('Deploy to Test Server') {
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
