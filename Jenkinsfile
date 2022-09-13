pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }

        stage('DeployToStagin') {
            when {
                branch 'master'
            }
            steps {
                withCredentials([usernamePassword(credentails: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')])
                    sshPublisher(
                        failOnError: true,
                        continueOnError: false,
                        publisher: (
                            sshPublisherDesc(
                                configName: 'staging',
                                sshCredentials: [
                                    username: "$USERNAME",
                                    encryptedPassPharse: "$USERPASS"
                                ]
                                transfers:(
                                    sshTransfer(
                                        sourceFile: 'dist/trainSchedule.zip',
                                        removeprefix: 'dist/',
                                        remoteDirectory: '/tmp',
                                        execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/train-schedule && sudo /usr/bin/systemctl start train-schedule'
                                    )
                                ) 
                            )
                        )
                    )
            }
        }
    }
}