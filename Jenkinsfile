pipeline {
    agent {
        label "master"
    }
    tools {
        maven "Maven"
    }
	stages {
		stage {
			step {
				sh 'echo "development environment"'
			}
		}
	}
	/*stages {
		stage("Check Out") {
			steps {
				script {
					git branch: 'main', credentialsId: 'Git_Cred', url: 'https://github.com/sessiondevops/nexus.git'
					
				}
			}
		}
		stage("Build") {
			steps {
				script {
					sh 'mvn clean install'
				}
			}
		} 
		stage('SonarQube analysis') {
			steps {
				script {
					//def scannerHome = tool 'sonarqube';
					withSonarQubeEnv("sonarqube") { // If you have configured more than one global server connection, you can specify its name
						sh "${scannerHome}/bin/sonar-scanner"
					}
				}
			}
		}
		 stage('Quality Gates'){
			steps {
				script {
					timeout(time: 1, unit: 'MINUTES') {
						def qg = waitForQualityGate() 
						if (qg.status != 'OK') {
							error "Pipeline aborted due to quality gate failure: ${qg.status}"
						}
					}
				}
			}
		}
		stage("Nexus Upload") {
			steps {
				script {
					def pom = readMavenPom file: ''
					//echo  "${projectArtifactId} ${projectVersion}"
					nexusArtifactUploader artifacts: [
						[
							artifactId: "${pom.artifactId}", 
							classifier: '', 
							file: "target/${pom.artifactId}-${pom.version}.war", 
							type: 'war'
						]
					], 
						credentialsId: 'Nexus_Cred', 
						groupId: "${pom.groupId}", 
						nexusUrl: '50.18.247.196:8081', 
						nexusVersion: 'nexus3', 
						protocol: 'http', 
						repository: 'et2-Snapshot', 
						version: "${pom.version}"
                }				
                    
            }
		}
		stage("Download Artificates") {
			steps {
				script {
					def pom = readMavenPom file: ''
					def workspace = WORKSPACE
					app_nexus_ver = $((curl -s -u admin:admin123 -X GET 'http://50.18.247.196:8081/service/rest/v1/search/assets?repository=et2-Snapshot&group=com.marsh&version=${pom.version}*&maven.extension=war') | grep version | awk '{print $3}' | sed 's/\"//g')
					sh "curl -u admin:admin123 -X GET 'http://50.18.247.196:8081/service/rest/v1/search/assets?repository=et2-Snapshot&group=${pom.groupId}&version=${app_nexus_ver}&maven.extension=war'"
					echo "Artifactes has been downloaded"
					//sh "mv $workspace/${pom.artifactId}.war /var/lib/tomcat/webapps/et2.war"
				}
			}
		}
		stage("Deploy") {
			steps {
				script {
					sh "sudo systemctl start tomcat"
				}
			}
		}
	}*/
	/*post {
        always {
            deleteDir()  clean up our workspace 
        }
	}*/
}
