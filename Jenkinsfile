pipeline {
    agent any
	tools {
		jdk 'JAVA_HOME'
		maven 'myMaven'
	}
	
	environment {
		PROJECT_ID = 'dockerorc'
                CLUSTER_NAME = 'gcpcluster-1'
                LOCATION = 'us-central1-c'
                CREDENTIALS_ID = 'k8_Cluster'		
	}
	
    stages {
	    stage('Scm Checkout') {
		    steps {
			    checkout scm
		    }
	    }
	    
	    stage('Build') {
		    steps {
			    sh 'mvn clean package'
		    }
	    }
	    
	    stage('Test') {
		    steps {
			    echo "Testing..."
			    sh 'mvn test'
		    }
	    }
	    
	    stage('Build Docker Image') {
		    steps {
			    sh 'whoami'
			    sh "sudo docker build -t timjar3/pubrepo1:$BUILD_NUMBER ."
			    
		    }
	    }
	    
	    stage("Push Docker Image") {
		    steps {
			    script {
				    echo "Push Docker Image"
				    sh "sudo docker push timjar3/pubrepo1:${env.BUILD_ID}" 
			
			    }
		    }
	    }
	    
	    stage('Deploy to K8s') {
		    steps{
			    echo "Deployment started ..."
			    sh 'ls -ltr'
			    sh 'pwd'
			    sh "sed -i 's/tagversion/${env.BUILD_ID}/g' serviceLB.yaml"
				sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml"
			    echo "Start deployment of serviceLB.yaml"
			    step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'serviceLB.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
				echo "Start deployment of deployment.yaml"
				step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
			    echo "Deployment Finished ..."
		    }
	    }
    }
}
