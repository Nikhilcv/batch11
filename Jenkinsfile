pipeline {
	agent none
	stages {
		stage ('build'){
			agent {
				label "master"
            }
			steps {
				git 'git@github.com:venkat09docs/batch10.git'
				withEnv(["PATH+MAVEN=${tool 'M3'}/bin"]) {
					sh "mvn clean package"		  
				}
				stash excludes: 'target/', includes: '**', name: 'source'
			}
		}
		stage ('test') {
			agent {
				label "master"
            }
			steps {
				parallel (
					'integration': { 
						unstash 'source'
						withEnv(["PATH+MAVEN=${tool 'M3'}/bin"]) {
							sh "mvn clean package"		  
						}		  						
					}, 'quality': {
						unstash 'source'
						withEnv(["PATH+MAVEN=${tool 'M3'}/bin"]) {
							sh "mvn clean package"		  
						}		  						
					}
				)
			}
		}
		stage ('approve') {
			agent {
				label "master"
            }
			steps {
				timeout(time: 7, unit: 'DAYS') {
					input message: 'Do you want to deploy?', submitter: 'admin'
				}
			}
		}
		stage ('deploy') {
			agent {
				label "master"
            }
			steps {
				unstash 'source'
				withEnv(["PATH+MAVEN=${tool 'M3'}/bin"]) {
					sh "mvn clean package"		  
				}				
			}
			post {
				always {
					archiveArtifacts '**/*.war'
				}
			}
		}
	}
}