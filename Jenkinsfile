/**
 * Declarative Pipeline for building the TomcatMavenApp.
 * This script automates the CI process: checkout, build, test, and artifact archiving.
 */
pipeline {
    // Defines where the entire pipeline will execute (any available agent/node)
    agent any

    // Ensure a Maven tool is configured in Jenkins under the name 'Maven'
    tools {
        maven 'Maven' 
    }

    // Start of the pipeline stages
    stages {
        
        stage('Checkout Source Code') {
            steps {
                // Explicitly check out the source code
                git url: 'https://github.com/pradeeprpin/TomcatMavenApp.git', branch: 'master'
            }
        }
        
        stage('Maven Build & Package') {
            steps {
                // Execute 'mvn clean package' which performs cleaning, compiling, testing, and packaging
                sh 'mvn clean package'
            }
        }
        
        stage('Verify Artifact') {
            steps {
                // Confirm the WAR file (TomcatMavenApp-2.0.war) was created
                sh 'ls -l target/TomcatMavenApp-2.0.war' 
            }
        }
        stage('Deploy via Docker') {
            steps {
                echo 'Stopping and removing old container C1...'
                // 1. Stop and remove any existing container named C1
                sh 'docker stop C1 || true'
                sh 'docker rm C1 || true'

                echo 'Starting new Tomcat container C1 on port 8001...'
                // 2. Run the Tomcat Docker container. 
                sh """
                docker run -d \\
                -p 8001:8080 \\
                -v ${WORKSPACE}/target/TomcatMavenApp-2.0.war:/usr/local/tomcat/webapps/TomcatMavenApp.war \\
                --name C1 tomcat:latest
                """
            }
        }

        stage('Archive Artifacts') {
            steps {
                // Archive the resulting WAR file to the Jenkins build record
                archiveArtifacts artifacts: 'target/TomcatMavenApp-*.war', fingerprint: true
            }
            stage('Verification') {
            steps {
                sleep 15 // Wait for Tomcat to fully unpack and start the application
                echo "Deployment complete. Access application at: http://YOUR_IP_ADDRESS:8001/TomcatMavenApp/"
            }
        }
    }
}
