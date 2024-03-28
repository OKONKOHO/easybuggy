pipeline{
    agent any 
    tools{
        maven "Maven_3_5_2"
    }
    stages{
        stage('CompileandRunSonarAnalysis') {
            steps {	
		    withCredentials([string(credentialsId: 'okonkohsec', variable: 'okonkohsec')]) {
    sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=okonkohsec -Dsonar.organization=okonkohsec -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=$okonkohsec'
}

		
			}
        } 
	    stage('RunSCAAnalysisUsingSnyk') {
            steps {		
				withCredentials([string(credentialsId: 'okonkohsnyk', variable: 'okonkohsnyk')]) {
					sh 'mvn snyk:test -fn'
				}
			}
    }	
        stage('Build'){
            steps{
                withDockerRegistry(
                    [credentialsId:"dockerlogin", url: ""]
                )  {
                    script{
                    app = docker.build("asg")
                    }
                }
            }
        }

        stage('Push'){
            steps{
                script{
                    docker.withRegistry("https://533266960414.dkr.ecr.us-east-1.amazonaws.com", "ecr:us-east-1:aws-cred"){
                        app.push("latest")
                    }
                    
          stage('Kubernetes Deployment of Easy Buggy Web Application') {
	     steps {
	       withKubeConfig([credentialsId: 'kubelogin']) {
		  sh('kubectl delete all --all -n devsecops')
		  sh ('kubectl apply -f deployment.yaml --namespace=devsecops')
		}
	      }
   	}
                    
                    
                }
            }
        }
    }

    
}
