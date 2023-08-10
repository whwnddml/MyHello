node {
    try{
        ////////////////////////////////////////////
        env.JAVA_HOME = "${tool 'jdk17'}"
        env.M3_HOME = "${tool 'maven-3.9.4'}"
        env.PATH = "${env.JAVA_HOME}/bin:${env.M3_HOME}/bin:${env.PATH}"

        stage('git checkout') {
            checkout scm
        }

        stage('maven build') {
            // 젠킨스 관리 > Managed Files 에 Config File 로 등록한 것을 사용함.
            configFileProvider([configFile(fileId: 'maven-settings-xml', variable: 'MVN_SETTINGS')]) {
            	sh "mvn -s $MVN_SETTINGS clean package"
            }

        }

        stage('SSH Transfer') {
            script {
                // Use the Publish Over SSH plugin to transfer files
                sshPublisher(publishers: [
                    sshPublisherDesc(configName: 'ds918', // jenkins ssh server 에 설정된 명칭
                    transfers: [
                        sshTransfer(cleanRemote: false,
                                    excludes: '',
                                    execCommand: 'sudo ls -lrt',  // Add the ls command to list files
                                    execTimeout: 120000,
                                    flatten: false,
                                    makeEmptyDirs: false,
                                    noDefaultExcludes: false,
                                    patternSeparator: '[, ]+',
                                    remoteDirectory: '/MyHello/lib', // SSH Server 에 기록된 Remote Directory 이후 경로만 입력.
                                    remoteDirectorySDF: false,
                                    removePrefix: 'target',
                                    sourceFiles: 'target/*.jar')
                        ],
                        usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)
                ])

                // SSH를 통해 원격 서버에서 JAR 파일 실행(경로이동포함)
                //sshCommand remote: remote, command: 'cd MyHello/lib'

            }
        }

        // 아래 코드는 성공. 결국 시놀로지 서버의 sudoers 의 NOPASSWD 를 입력하고 나서야 sudo 명령으로 처리 됨.
        stage('Remote SSH') {
            withCredentials([usernamePassword(credentialsId: 'DS918-ssh', usernameVariable: 'userName', passwordVariable: 'password')]) {
                def remote = [:]
                remote.name = "DS918-ssh" // jenkins Credential ID
                remote.host = "junny.dyndns.org"
                remote.port = 2223
                remote.allowAnyHosts = true
                remote.user = userName
                remote.password = password

                // 서버 명령
                // Multiple commands separated by && operator
                def combinedCommand = 'sudo ls -al && cd workspace/MyHello && sh ./bin/restart.sh'

                // Execute the combined command
                sshCommand remote: remote, command: combinedCommand
            }
        }

    }catch(err){
        echo "Build Fail!!"
        throw err
    }

    echo "Build Success!!"

}