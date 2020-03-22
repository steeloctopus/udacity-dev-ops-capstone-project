pipeline {
    environment {
        dockerHubCredentials = 'Docker Hub'
    }
    agent any
        stages {
            stage('Start Pipeline') {
                steps {
                    sh 'echo "This is the first step of the build"'
                    sh 'echo "Start Build"'
                     }
            }
            stage('Get GIT_HASH') {
                        steps {
                            script {
                                env.GIT_HASH = sh(
                                    script: "git show --oneline | head -1 | cut -d' ' -f1",
                                    returnStdout: true
                                ).trim()
                               sh 'echo ${env.GIT_HASH}'
                            }
                        }
                    }
            stage('Lint Dockerfile'){
                steps {
                    sh 'echo "Linting Docker File"'
                    retry(2){
                        sh 'wget -O hadolint https://github.com/hadolint/hadolint/releases/download/v1.17.5/hadolint-Linux-x86_64 &&\
                                    chmod +x hadolint'
                        sh 'make lint'
                    }
                }
            }
            stage('Build & Push to Dockerfile') {
                        steps {
                            script {
                                sh 'echo "Build Docker Image"'
                                dockerImage = docker.build("steeloctopus/duckhunt:${env.GIT_HASH}")
                                sh 'echo "Push Docker Image"'
                                retry(2){
                                docker.withRegistry('',dockerHubCredentials ) {
                                dockerImage.push()
                                    }
                                }
                            }
                        }
                    }

//             stage('Publish to S3') {
//                 steps {
//                 withAWS(region: 'us-east-1', credentials: 'Jenkins') {
//                           sh 'echo "Uploading content with AWS creds"'
//                           s3Upload(pathStyleAccessEnabled: true, payloadSigningEnabled: true, file: 'index.html', bucket: 'udacity-dev-ops-project-three')
//                           s3Upload(pathStyleAccessEnabled: true, payloadSigningEnabled: true, file: 'IMG_0809.jpg',
//                            bucket: 'udacity-dev-ops-project-three')
//                         }
//                 }
//             }
        }
    post {
            always {
                echo 'Jenkins starting deployment'
            }
            success {
                echo 'The deployment has been successfully run'
            }
            failure {
                echo 'Deployment failed'
            }
            unstable {
                echo 'Something went wrong, unstable state'
            }
            changed {
                echo 'Pipeline has been changed'
            }
        }
}