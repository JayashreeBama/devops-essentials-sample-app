pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                bat 'gradlew.bat build' // Windows equivalent of './gradlew build'
                archiveArtifacts artifacts: 'src\\index.html' // Windows path
            }
        }

        stage('DeployToStage') {
            
            steps {
                withCredentials([string(credentialsId: 'cloud_user_pw', variable: 'USERPASS')]) {
                    sshPublisher(
                        failOnError: true,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'staging',
                                sshCredentials: [
                                    username: 'cloud_user',
                                    encryptedPassphrase: "$USERPASS"
                                ], 
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: 'src\\**', // Windows-style path
                                        removePrefix: 'src\\'
                                    )
                                ]
                            )
                        ]
                    )
                }
            }
        }

        stage('DeployToProd') {
            
            steps {
                input 'Does the staging environment look OK?'
                milestone(1)
                withCredentials([string(credentialsId: 'cloud_user_pw', variable: 'USERPASS')]) {
                    sshPublisher(
                        failOnError: true,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'production',
                                sshCredentials: [
                                    username: 'cloud_user',
                                    encryptedPassphrase: "$USERPASS"
                                ], 
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: 'src\\**',
                                        removePrefix: 'src\\'
                                    )
                                ]
                            )
                        ]
                    )
                }
            }
        }
    }
}
