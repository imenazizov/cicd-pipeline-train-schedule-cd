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
        stage('staging') {
            when {
              branch 'master'
            }
            steps {
             sshPublisher(
               publishers: [
                  sshPublisherDesc(
                           configName: 'staging',
                           sshCredentials: [encryptedPassphrase: '{AQAAABAAAAAQj2YFxuQgVCm1xOHC/ip8v08RjY8J+2Wm3EoSRfSmvqc=}',
                           key: '', 
                           keyPath: '',
                           username: 'deploy'], 
               transfers: [
                  sshTransfer(
                      excludes: '',
                      execCommand: 'sudo systemctl stop train-schedule; rm -rf  /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo systemctl start train-scheduler',
                      execTimeout: 120000,
                      flatten: false,
                      makeEmptyDirs: false,
                      noDefaultExcludes: false,
                      patternSeparator: '[, ]+',
                      remoteDirectory: '/tmp',
                      remoteDirectorySDF: false,
                      removePrefix: 'dist/',
                      sourceFiles: 'dist/trainScheduler.zip'
                  )
               ],
                usePromotionTimestamp: false,
                useWorkspaceInPromotion: false,
                verbose: false
                )
              ]
            )
          }
        }
        stage('production') {
            when {
              branch 'master'
            }
            steps {
              input "Good to deploy to production?"
                milestone(1)
                sshPublisher(
                    publishers: [
                        sshPublisherDesc(
                            configName: 'production',
                            sshCredentials: [encryptedPassphrase: '{AQAAABAAAAAQj2YFxuQgVCm1xOHC/ip8v08RjY8J+2Wm3EoSRfSmvqc=}',
                                             key: '',
                                             keyPath: '',
                                             username: 'deploy'],
                            transfers: [sshTransfer(excludes: '',
                                                    execCommand: 'sudo systemctl stop train-schedule; rm -rf  /opt/train-schedule/* && /tmp/unzip trainSchedule.zip -d /opt/train-schedule && sudo systemctl start train-scheduler',
                                                    execTimeout: 120000,
                                                    flatten: false,
                                                    makeEmptyDirs: false,
                                                    noDefaultExcludes: false,
                                                    patternSeparator: '[, ]+',
                                                    remoteDirectory: '/tmp',
                                                    remoteDirectorySDF: false,
                                                    removePrefix: 'dist/',
                                                    sourceFiles: 'dist/trainScheduler.zip'
                                                   )
                                       ],
                            usePromotionTimestamp: false,
                            useWorkspaceInPromotion: false,
                            verbose: false
                        )
                    ]
                )
            }
        }
    }
}
