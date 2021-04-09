pipeline {
    agent {
        label "master"
    }
    environment {
    	GIT_Sample='https://github.com/sessiondevops/sample.git'
	GIT_Nexus='https://github.com/sessiondevops/nexus.git'
  	}
	stages {
		stage ("git checkout") {
			steps {
				git branch: 'development', credentialsId: 'Git_Cred', url: '${env.GIT_Sample}'
				git branch: 'development', credentialsId: 'Git_Cred', url: '${env.GIT_Nexus}'
				echo 'Pulling...' + env.BRANCH_NAME
			}
		}
	}
}
