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
            	sh "mvn -s $MVN_SETTINGS ..."
            }
        }

    }catch(err){
        echo "Build Fail!!"
        throw err
    }

    echo "Build Success!!"

}