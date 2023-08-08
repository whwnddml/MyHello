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
                // Define SSH connection details
                def remote = [
                    name: 'ds918',
                    host: 'junny.dyndns.org',
                    username: 'jenkins',
                    password: 'DS918-ssh', // Use credentials ID for security
                    sourceFiles: 'var/jenkins_home/workspace/MyHello-Pipeline/target/*.jar', // Path to files to upload
                    remoteDirectory: 'workspace/MyHello/lib' // Destination directory on the remote server
                ]

                // Use the Publish Over SSH plugin to transfer files
                sshPublisher(publishers: [
                    sshPublisherDesc(configName: 'ds918',
                    transfers: [
                        sshTransfer(cleanRemote: false,
                                    excludes: '',
                                    execCommand: 'echo ttttttttttttt',
                                    execTimeout: 120000,
                                    flatten: false,
                                    makeEmptyDirs: false,
                                    noDefaultExcludes: false,
                                    patternSeparator: '[, ]+',
                                    remoteDirectory: '/workspace/MyHello/lib/',
                                    remoteDirectorySDF: false,
                                    removePrefix: 'workspace/MyHello-Pipeline/target',
                                    sourceFiles: 'workspace/MyHello-Pipeline/target/*.jar')
                        ],
                        usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)
                ])
            }
        }


    }catch(err){
        echo "Build Fail!!"
        throw err
    }

    echo "Build Success!!"

}