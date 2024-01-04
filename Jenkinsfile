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
            cat > payload.json <<__HERE__
{
  "project": "webgoat",
  "scan": "$(cat dependency-check-report.xml |base64 -w 0 -)"
}
    }
        
            stage('dependencyTrackPublisher') {
            steps {
                withCredentials([string(credentialsId: 'api-key', variable: 'API_KEY')]) {
                    dependencyTrackPublisher artifact: 'payload.json', projectName: 'webgoat', projectVersion: '1', synchronous: true, dependencyTrackApiKey: API_KEY
                }
            }
    
    }
}
}
