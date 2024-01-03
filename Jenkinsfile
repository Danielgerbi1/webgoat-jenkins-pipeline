pipeline {
    agent any

    stages {
        stage('Clone Repo') {
            steps {
                cleanWs()
                sh 'git clone https://github.com/WebGoat/WebGoat -b v8.1.0 .'
            }
        }
        stage('Build Webgoat') {
            steps {
                sh './mvnw clean install -DskipTests'
            }
        }
        stage('OWASP Dependency-Check Vulnerabilities') {
            steps {
        dependencyCheck additionalArguments: ''' 
                    -o './'
                    -s './'
                    -f 'ALL' 
                    --prettyPrint''', odcInstallation: 'owasp'
      }
    }
            stage('dependencyTrackPublisher') {
            steps {
                withCredentials([string(credentialsId: 'api-key', variable: 'API_KEY')]) {
                    dependencyTrackPublisher artifact: 'var/jenkins_home/workspace/webgoat/./dependency-check-report.xml', projectName: 'webgoat', projectVersion: '1', synchronous: true, dependencyTrackApiKey: API_KEY
                }
            }
    
    }
}
}
