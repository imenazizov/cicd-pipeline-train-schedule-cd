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
          sshPublisher(
              publishers: [
                  sshPublisherDesc(
                           configName: 'staging',
                           sshCredentials: [encryptedPassphrase: '{AQAAABAAAAAQ+P+v4nWpcgFy/k1oFb6IEmO1Qi54/Ta55EZ9EN0Ps6c=}',
                           key: '', 
                           keyPath: '',
                           username: 'deploy'], 
              transfers: [
                  sshTransfer(
                      excludes: '',
                      execCommand: 'cd /tmp && systemctl stop train-schedule && rm -rf  /opt/train-schedule && unzip trainScheduler.zip -d /opt/train-schedule && systemctl start train-scheduler',
                      execTimeout: 120000,
                      flatten: false,
                      makeEmptyDirs: false,
                      noDefaultExcludes: false,
                      patternSeparator: '[, ]+',
                      remoteDirectory: '/tmp',
                      remoteDirectorySDF: false,
                      removePrefix: 'dist',
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
        stage('production') {
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
                                                    execCommand: 'cd /tmp && systemctl stop train-schedule && rm -rf  /opt/train-schedule && unzip trainScheduler.zip -d /opt/train-schedule && systemctl start train-scheduler',
                                                    execTimeout: 120000,
                                                    flatten: false,
                                                    makeEmptyDirs: false,
                                                    noDefaultExcludes: false,
                                                    patternSeparator: '[, ]+',
                                                    remoteDirectory: '/tmp',
                                                    remoteDirectorySDF: false,
                                                    removePrefix: 'dist',
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
