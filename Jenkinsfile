pipeline {

    agent { label 'slave1' }

	environment {	
		DOCKERHUB_CREDENTIALS=credentials('dockerloginid3')
	}
	
    stages {
        stage('SCM_Checkout') {
            steps {
                echo 'Perform SCM Checkout'
				git 'https://github.com/pallavi2496/insurance-project.git'
            }
        }
        stage('Application Build') {
            steps {
                echo 'Perform Application Build'
				sh 'mvn clean package'
				
            }
        }
        stage('Docker Build') {
            steps {
                echo 'Perform Docker Build'
				sh "docker build -t pallavi2320/insurance-app-img:${BUILD_NUMBER} ."
			    sh "docker tag pallavi2320/insurance-app-img:${BUILD_NUMBER} pallavi2320/insurance-app-img:latest"
				sh 'docker image list'
            }
        }
        stage('Login to Dockerhub') {
            steps {
                echo 'Login to DockerHub'				
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u pallavi2320 --password-stdin'
                
            }
        }
        stage('Publish the Image to Dockerhub') {
            steps {
                echo 'Publish to DockerHub'
				sh "docker push pallavi2320/insurance-app-img:latest"                
            }
        }
        stage('Deploy to Kubernetes Cluster') {
            steps {
				script {
		     		sshPublisher(publishers: [sshPublisherDesc(configName: 'Kubernetes_Master1', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'kubectl apply -f kubernetesdeploy.yaml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '.', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '*.yaml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
		    	}				          
            }
        }
    }
}			
    
    stage('Pushing it ot the DockerHub'){
        echo 'Pushing the docker image to DockerHub'
        withCredentials([string(credentialsId: 'dock-password', variable: 'dockerHubPassword')]) {
        sh "${dockerCMD} login -u shubhamkushwah123 -p ${dockerHubPassword}"
        sh "${dockerCMD} push shubhamkushwah123/insure-me:${tagName}"
            
        }
        
    stage('Configure and Deploy to the test-server'){
        ansiblePlaybook become: true, credentialsId: 'ansible-key', disableHostKeyChecking: true, installation: 'ansible', inventory: '/etc/ansible/hosts', playbook: 'ansible-playbook.yml'
    }
        
        
    }
}




