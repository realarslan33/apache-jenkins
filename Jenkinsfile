pipeline {
    agent any
    
    environment {
        DEPLOY_PATH = '/var/www/html'  // Apache default path
        BRANCH_NAME = "${env.BRANCH_NAME}"
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Deploy to Apache') {
            steps {
                script {
                    def deployPath = "${DEPLOY_PATH}/${BRANCH_NAME}"
                    
                    // Create branch-specific directory if it doesn't exist
                    sshPublisher(
                        publishers: [
                            sshPublisherDesc(
                                configName: 'AWS_Apache',
                                transfers: [
                                    sshTransfer(
                                        execCommand: """
                                            mkdir -p ${deployPath}
                                            chmod 755 ${deployPath}
                                        """
                                    )
                                ]
                            )
                        ]
                    )
                    
                    // Deploy HTML file
                    sshPublisher(
                        publishers: [
                            sshPublisherDesc(
                                configName: 'AWS_Apache',
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: '*.html',
                                        removePrefix: '',
                                        remoteDirectory: "${BRANCH_NAME}",
                                        execCommand: """
                                            chmod 644 ${deployPath}/*.html
                                            echo "Deployed to ${deployPath}"
                                        """
                                    )
                                ]
                            )
                        ]
                    )
                }
            }
        }
    }
    
    post {
        success {
            echo "Deployment successful! Access the site at:"
            echo "Main branch: http://54.165.184.226/main/"
            echo "Development branch: http://54.165.184.226/development/"
        }
        failure {
            echo "Deployment failed!"
        }
    }
}
