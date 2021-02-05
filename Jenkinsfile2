node{
    
    stage('GitHub Stage'){
        git branch: 'master', credentialsId: 'hub-creds', url: 'https://github.com/dev-rahman96/devbops_event_microservice.git'
    }

    // stage('Pre reqs for build'){
    //     sh 'curl -O https://bootstrap.pypa.io/get-pip.py'
    //     sh 'python3 get-pip.py'
    //     sh 'python3 -m pip install flask'
    //     sh 'python3 -m pip install boto3'
    // }


    stage('Testing the event microservice'){
        sh 'python3 test_Events.py'
    }
    
    stage('Im building the docker image'){
        sh 'docker build -t devrahman96/event-micro .'
    
    }

    stage('Im pushing your image'){

        withCredentials([string(credentialsId: 'Docker-creds', variable: 'dockerHubPwd')]) {
            sh "docker login -u devrahman96 -p ${dockerHubPwd}"
    
            }
        
        sh 'docker push devrahman96/event-micro'
    
    }

    stage('Run Docker Container on Private EC2'){
        def dockerRm = 'docker rm -f event-micro'
        def dockerRmI = 'docker rmi devrahman96/event-micro'
        def dockerRun = 'docker run -p 8080:80 -d --name event-micro devrahman96/event-micro'
        sshagent(['remote-server']) {
            sh "ssh -o StrictHostKeyChecking=no ec2-user@172.25.11.222 ${dockerRm}"
            sh "ssh -o StrictHostKeyChecking=no ec2-user@172.25.11.222 ${dockerRmI}"
            sh "ssh -o StrictHostKeyChecking=no ec2-user@172.25.11.222 ${dockerRun}"
           
        }
    
    }   
}
