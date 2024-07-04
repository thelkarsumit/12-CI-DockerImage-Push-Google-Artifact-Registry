pipeline {
    agent any
    
    environment {
        SCANNER_HOME=tool 'SonarQube-Scanner'
        PROJECT_ID='peak-axiom-426310-b1'
        CLIENT_EMAIL='jenkins-vm-controller@peak-axiom-426310-b1.iam.gserviceaccount.com'
        GCLOUD_CREDS=credentials('GCP-service-key')
        IMAGE_NAME = 'hello-world'
        DOCKER_IMAGE = ''
      }
    
tools {
        maven 'Maven'
    } 
stages {    
    stage('SonarQube Analysis') { 
            steps {
                withSonarQubeEnv('sonar') { 
                    sh '''
                       mvn clean verify sonar:sonar \
                      -Dsonar.projectKey=CI-DockerImage-Push-Google-Artifact-Registry \
                      -Dsonar.host.url=http://34.71.56.57:9000 \
                      -Dsonar.login=sqp_5059067c2d0f656f42322bd607423bdfb7cb7e74
                       '''
                }
            }
        }
stage('Maven Test') {
            steps {
                // Define steps for the Test stage
                echo 'Running tests...'
                sh  'mvn test'
            }
        }
    
stage('Maven Build') {
            steps {
                // Define steps for the Build stage
                echo 'Building the project...'
                sh 'mvn clean package'
            }
        }
    
stage('Docker Build Image') {
            steps {
                script {
                    // Build the Docker image
                    sh 'docker build -t us-central1-docker.pkg.dev/peak-axiom-426310-b1/docker-image-push-01/helloworld1 .'
                }
            }
        }
    
stage('Trivy Scan Docker Image') {
            steps {
                script {
                    sh 'trivy image us-central1-docker.pkg.dev/peak-axiom-426310-b1/docker-image-push-01/helloworld1 > trivy_report.txt'
                }
            }
        }
    
stage('Docker Push To Google-Artifact-Registry') {
            steps {
                script {
                        sh 'gcloud auth configure-docker us-central1-docker.pkg.dev'
                        sh 'docker push us-central1-docker.pkg.dev/peak-axiom-426310-b1/docker-image-push-01/helloworld1'
                }
            }
        }
}    
post {
        success {
            // Actions to take if the pipeline succeeds
            echo 'Pipeline succeeded!'
            // You can also send an email notification on success
            mail to: 'thelkarsc@gmail.com',
                 subject: "Pipeline Succeeded: ${currentBuild.fullDisplayName}",
                 body: "The pipeline ${env.BUILD_URL} has successfully completed."
        }
        failure {
            // Actions to take if the pipeline fails
            echo 'Pipeline failed!'
            // You can also send an email notification on failure
            mail to: 'thelkarsc@gmail.com',
                 subject: "Pipeline Failed: ${currentBuild.fullDisplayName}",
                 body: "The pipeline ${env.BUILD_URL} has failed. Check the logs for details."
        }
    }  
}
