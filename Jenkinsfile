def pom 
def nexusRepoName
pipeline{
    agent any
    tools{
        maven "Maven"
    }
    environment { 
        registry = "sessiondevops/multi" 
        registryCredential = 'Docker_Cred'
        dockeImage = ''
        Nexus_URL = "http://54.176.18.106:8081"
    }
    stages
    {
        stage("GIT"){
            steps{
                git credentialsId: 'Git_Cred', url: 'https://github.com/sessiondevops/nexus.git'
                //git branch: 'main', credentialsId: 'Git_Cred', url: 'https://github.com/sessiondevops/nikhil.git'
            }
        }
        stage("Sonarqube_Chek"){
           steps{
                script{
                    def scannerHome = tool 'sonarqube';
                    withSonarQubeEnv("sonarqube"){
                        sh "${scannerHome}/bin/sonar-scanner"
                    }
                }
           }   
        }
        stage("Quality Gate Check"){
            steps{
                script{
                    timeout(time: 3, unit: 'MINUTES'){
                        def qg = waitForQualityGate();
                        if (qg.status != 'OK'){
                            error "Pipleline aborted due to QG failure: ${qg.status}"
                        }
                        
                    }
                }
                
            }
        }
        stage("Maven"){
            steps{
                sh 'mvn clean install'
            }
        }
        stage("Nexus Artifact upload"){
            steps{
                script{ 
                    pom = readMavenPom file : ''
                    nexusRepoName = pom.version.endsWith("SNAPSHOT") ? "${pom.artifactId}-snapshot" : "${pom.artifactId}-release"
                    nexusArtifactUploader artifacts: [[artifactId: "${pom.artifactId}", classifier: '', file: "target/${pom.artifactId}-${pom.version}.${pom.packaging}", type: "${pom.packaging}"]], credentialsId: 'Nexus_Cred', groupId: "${pom.groupId}", nexusUrl: '${Nexus_URL}', nexusVersion: 'nexus3', protocol: 'http', repository: nexusRepoName, version: "${pom.version}"
                    echo "${nexusRepoName}"
                }
            }
        }
        stage("Download war file"){
            steps{
                script{
                    echo "${pom.artifactId}-release"
                    if ( nexusRepoName == "${pom.artifactId}-release" )
                    {
                    //def pom = readMavenPom file : ''
                        sh "curl -u admin:admin '${Nexus_URL}/repository/${nexusRepoName}/com/marsh/${pom.artifactId}/${pom.version}/${pom.artifactId}-${pom.version}.war' -o ${pom.artifactId}.war"
                    } else {
                        sh "curl -u admin:admin '${Nexus_URL}/repository/${nexusRepoName}/com/marsh/$pom.artifactId/$pom.version/maven-metadata.xml' | grep value > app1.txt" 
                        sh "cat app1.txt | sed 's/ //g' > spa.txt"
                        sh "sed -e 's/<[^>]*>//g' < spa.txt > tagg.txt"
                        sh "truncate -s -1 tagg.txt"
                        env.Filename = readFile 'tagg.txt'
                        sh "curl -u admin:admin '${Nexus_URL}/repository/${nexusRepoName}/com/marsh/${pom.artifactId}/${pom.version}/${pom.artifactId}-${Filename}.war' -o ${pom.artifactId}.war"
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
        /*stage('Cleaning up') { 
            steps { 
                sh "docker rmi $registry:$BUILD_NUMBER" 
            }
        }*/
        stage("K8 Nodes"){
            steps{
                kubernetesDeploy(
                    configs: 'mongo-deployment.yaml',
                    kubeconfigId: 'K8_Creds',
                    //enableConfigsubstituion: true
                    enableConfigSubstitution: true
                )
            }
        }
    }
}
