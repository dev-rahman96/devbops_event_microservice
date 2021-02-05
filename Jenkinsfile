def getVersion(){
     def commitHash = sh returnStdout: true, script: 'git rev-parse --short HEAD'
     return commitHash

}
node{
    environment {
       DOCKER_TAG = "getVersion()"
    }
    stage('GitHub Stage'){
        git branch: 'master', credentialsId: 'hub-creds', url: 'https://github.com/dev-rahman96/devbops_event_microservice.git'
    }

     stage('Pre reqs for build'){
         sh 'curl -O https://bootstrap.pypa.io/get-pip.py'
         sh 'python3 get-pip.py'
         sh 'python3 -m pip install flask'
         sh 'python3 -m pip install boto3'
     }


    stage('Testing the event microservice'){
        sh 'python3 test_Events.py'
    }
    
    
    stage('Im building the docker image'){
        sh 'docker build . -t devrahman96/eventwithansible${DOCKER_TAG} '
    
    }

    stage('Im pushing your image'){

        withCredentials([string(credentialsId: 'Docker-creds', variable: 'dockerHubPwd')]) {
            sh "docker login -u devrahman96 -p ${dockerHubPwd}"
    
            }
        
        sh 'docker push devrahman96/eventwithansible${DOCKER_TAG}'
    
    }

    stage('Im using ansible to build your container'){
        
            ansiblePlaybook credentialsId: 'remote-server', disableHostKeyChecking: true, extras: '-e DOCKER_TAG=${DOCKER_TAG}', installation: 'ansible', inventory: 'dev.inv', playbook: 'deploy-docker.yml'
        
    }

}