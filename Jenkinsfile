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
stage('SonarQube Analysis') { 
            steps {
                withSonarQubeEnv('SonarQube-server') { 
                    sh '''
                         mvn clean verify sonar:sonar \
                        -Dsonar.projectKey=CI-DockerImage-Push-Google-Artifact-Registry \
                        -Dsonar.host.url=http://35.228.122.206:9000 \
                        -Dsonar.login=sqp_9a7b322e5ebb580b366e0c50bff6542136d3577e
                       '''
                }
            }
        }    
stage('Docker Build Image') {
            steps {
                script {
                    // Build the Docker image
                    sh 'docker build -t us-central1-docker.pkg.dev/peak-axiom-426310-b1/docker-image-push-01/helloworld2 .'
                }
            }
        }
    
stage('Trivy Scan Docker Image') {
            steps {
                script {
                    sh 'trivy image us-central1-docker.pkg.dev/peak-axiom-426310-b1/docker-image-push-01/helloworld > trivy_report.txt'
                }
            }
        }
    
 stage('Docker Push To Google-Artifact-Registry') {
            steps {
                script { 
                        sh 'gcloud auth activate-service-account --key-file="$GCLOUD_CREDS" ' 
                        sh 'gcloud auth configure-docker us-central1-docker.pkg.dev'
                        sh 'docker push us-central1-docker.pkg.dev/peak-axiom-426310-b1/docker-image-push-01/helloworld2'
                }
            }
       }
}
         post {
          always {
                 cleanWs()
            }
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
}
