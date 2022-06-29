pipeline { 
// 
    environment { 
        registry = "imalka03/node-js-demo" 
        registryCredential = 'Dockerhub_id' 
        dockerImage = '' 
    }

    agent any

    stages { 
        stage('Cloning our Git') { 
            steps { 
                 git branch: 'main',
                 credentialsId: 'Git-Token',
                 url: 'https://github.com/ict17860/jenkins-pipeline.git'
                // git 'https://github.com/ict17860/jenkins-pipeline.git' 
            }

            // steps { 
            //     git 'https://github.com/dimuit86/jenkins-pipeline.git' 
            // }
        } 
        
        stage('Code Quality Check via SonarQube') {
			steps {
				script {
				def scannerHome = tool 'SonarqubeScanner';
					withSonarQubeEnv("SonarqubeServer") {
					sh "${scannerHome}/bin/sonar-scanner \
						-Dsonar.projectKey=node-js-react-npm-app \
						-Dsonar.sources=. \
						-Dsonar.css.node=. \
						-Dsonar.host.url=http://jenkinsdemo.eastus.cloudapp.azure.com:9000/ \
						-Dsonar.login=squ_0b93271d770512d7237aa6773aa443d3e2b7202c"
					}

				}
			}
		}
        
        stage('Building our image') { 
            steps { 
                script { 
                    dockerImage = docker.build registry + ":$BUILD_NUMBER" 
                }
            } 
        }
        stage('Deploy our image') { 
            steps { 
                script { 
                    docker.withRegistry( '', registryCredential ) { 
                        dockerImage.push() 
                    }
                } 
            }
        } 
        stage('Cleaning up') { 
            steps { 
                sh "docker rmi $registry:$BUILD_NUMBER" 
            }
        } 
    }
}
