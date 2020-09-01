pipeline {
    agent any
    stages {
        stage('Build Application') {
            steps {
                sh '/opt/apache-maven-3.6.3/bin/mvn -f java-tomcat-sample/pom.xml clean package'
            }
            post {
                success {
                    echo "Now Archiving the Artifacts...."
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }
    }
}
