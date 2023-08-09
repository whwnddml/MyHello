node {

    try{

        // def JAVA_HOME = "${tool 'adoptopenjdk8'}"
        // def M3_HOME = "${tool 'maven3.8.4'}"
        // withEnv ([
        //     "JAVA_HOME=${JAVA_HOME}",
        //     "M3_HOME=${M3_HOME}",
        //     "PATH=${JAVA_HOME}/bin:${M3_HOME}/bin:${env.PATH}"]){
        //     //...
        // }
        ////////////////////////////////////////////
        env.JAVA_HOME = "${tool 'jdk17'}"
        env.M3_HOME = "${tool 'maven-3.9.4'}"
        env.PATH = "${env.JAVA_HOME}/bin:${env.M3_HOME}/bin:${env.PATH}"


        stage('git checkout') {
//             git (
//                 branch: "master",
//                 credentialsId: "github-ssh",
//                 url: "git@github.....git"
//             )
            checkout scm
        }

        stage('maven build') {

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
                                    execCommand: 'java -jar -Dserver.port=18081 *.jar &',  // Add the ls command to list files
                                    execTimeout: 120000,
                                    flatten: false,
                                    makeEmptyDirs: false,
                                    noDefaultExcludes: false,
                                    patternSeparator: '[, ]+',
                                    remoteDirectory: '/MyHello/lib',
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

        /*
        def remote = [:]
        remote.name = 'DS918-ssh'
        remote.host = 'junny.dyndns.org'
        remote.user = 'jenkins'
        remote.password = 'DS918-ssh'
        remote.allowAnyHosts = true

        stage('Remote SSH') {
            sshCommand remote: remote, command: "ls -lrt"
            sshCommand remote: remote, command: "for i in {1..5}; do echo -n \"Loop \$i \"; date ; sleep 1; done"
        }
        // 이렇게 하니 Auth fail 이 발생함.
        */
        withCredentials([usernamePassword(credentialsId: 'DS918-ssh', usernameVariable: 'userName', passwordVariable: 'password')]) {
            def remote = [:]
            remote.name = "DS918-ssh"
            remote.host = "junny.dyndns.org"
            remote.allowAnyHosts = true
            remote.user = userName
            remote.password = password

            // 서버 재시작 명령
            sshCommand remote: remote, command: 'ls -al'
        }


    }catch(err){
        echo "Build Fail!!"
        throw err
    }

    echo "Build Success!!"

}