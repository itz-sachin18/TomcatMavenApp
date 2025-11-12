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

        stage('Archive Artifacts') {
            steps {
                // Archive the resulting WAR file to the Jenkins build record
                archiveArtifacts artifacts: 'target/TomcatMavenApp-*.war', fingerprint: true
            }
        }
    }
}
