pipeline {
	agent none
	stages {
		stage ('build'){
			agent {
				label "slave"
            }
			steps {
				git 'https://github.com/Nikhilcv/batch11.git'
				withEnv(["PATH+MAVEN=${tool 'M2'}/bin"]) {
					sh "mvn clean package"		  
				}
				stash excludes: 'target/', includes: '**', name: 'source'
			}
		}
		stage ('test') {
			agent {
				label "slave"
            }
			steps {
				parallel (
					'integration': { 
						unstash 'source'
						withEnv(["PATH+MAVEN=${tool 'M2'}/bin"]) {
							sh "mvn clean package"		  
						}		  						
					}, 'quality': {
						unstash 'source'
						withEnv(["PATH+MAVEN=${tool 'M2'}/bin"]) {
							sh "mvn clean package"		  
						}		  						
					}
				)
			}
		}
		stage ('approve') {
			agent {
				label "slave"
            }
			steps {
				timeout(time: 7, unit: 'DAYS') {
					input message: 'Do you want to deploy?', submitter: 'nik'
				}
			}
		}
		stage ('deploy') {
			agent {
				label "slave"
            }
			steps {
				unstash 'source'
				withEnv(["PATH+MAVEN=${tool 'M2'}/bin"]) {
					sh "mvn clean install"		  
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
