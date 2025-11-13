/**
 * Final Declarative Pipeline for building and deploying the TomcatMavenApp 
 * using Maven and Docker. This script implements Continuous Integration (CI)
 * and Continuous Delivery (CD).
 */
pipeline {
    // Defines where the entire pipeline will execute (any available agent/node)
    agent any

    tools {
        // IMPORTANT: Ensure 'Maven' is the name configured in Jenkins Global Tool Configuration.
        maven 'Maven'  
    }

    environment {
        // Define key variables for clean scripting
        WAR_FILE = 'TomcatMavenApp-2.0.war'
        CONTAINER_NAME = 'C1'
        HOST_PORT = '8001'
    }

    stages {
        
        stage('Checkout Source Code') {
            steps {
                echo 'Checking out source code...'
                // IMPORTANT: This URL was present in your previous logs. Ensure this is the correct source repository.
                git url: 'https://github.com/pradeeprpin/TomcatMavenApp.git', branch: 'master'
            }
        }
        
        stage('Maven Build & Package') {
            steps {
                echo 'Starting Maven build (mvn clean package)...'
                sh 'mvn clean package'
            }
        }
        
        stage('Verify Artifact') {
            steps {
                echo "Verifying existence of target/${WAR_FILE}"
                sh "ls -l target/${WAR_FILE}" 
            }
        }

        stage('Deploy via Docker') {
            steps {
                echo 'Starting deployment to Dockerized Tomcat on port 8001...'
                
                // 1. Stop and remove any previous container to allow a clean restart
                sh "docker stop ${CONTAINER_NAME} || true"
                sh "docker rm ${CONTAINER_NAME} || true"

                // 2. Run the Tomcat Docker container. 
                // Maps container port 8080 to host port 8001.
                sh """
                docker run -d \\
                -p ${HOST_PORT}:8080 \\
                -v ${WORKSPACE}/target/${WAR_FILE}:/usr/local/tomcat/webapps/TomcatMavenApp.war \\
                --name ${CONTAINER_NAME} tomcat:latest
                """
            }
        }

        stage('Archive Artifacts') {
            steps {
                echo "Archiving ${WAR_FILE} to Jenkins build record."
                archiveArtifacts artifacts: "target/${WAR_FILE}", fingerprint: true
            }
        }
        
        // This stage is now correctly positioned as a sibling stage.
        stage('Verification & Access Info') {
            steps {
                // Wait for Tomcat to fully unpack and start the application
                sleep 20 
                echo "Deployment FINISHED. Check the URL below."
                echo "Access application at: http://YOUR_PUBLIC_IP:${HOST_PORT}/TomcatMavenApp/"
            }
        }
    }
}
