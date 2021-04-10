pipeline {
    agent {
        label "master"
    }
	stages {
		stage ("git checkout") {
			steps {
				git branch: 'development', credentialsId: 'Git_Cred', url: 'https://github.com/sessiondevops/sample.git'
				git branch: 'development', credentialsId: 'Git_Cred', url: 'https://github.com/sessiondevops/nexus.git'
				echo 'Pulling...' + env.BRANCH_NAME
			}
		}
	}
}
