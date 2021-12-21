pipeline {
    agent any
    tools {
        maven 'Maven'
    }

    stages {

        stage('Build Maven') {
            steps{
                 git branch: 'master', credentialsId: 'Github-token', url: 'https://github.com/HoaiNguyenofficial/DevSecOpsMilestone'
                 sh "mvn -Dmaven.test.failure.ignore=true clean package"
                
            }
        } 
        
        // stage('sonar-scanner analysis') {
        //   def sonarqubeScannerHome = tool name: 'SonarQubeScanner-4.6.2', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
        //   withCredentials([string(credentialsId: 'SonarQube', variable: 'sonarLogin')]) {
        //   sh "${sonarqubeScannerHome}/bin/sonar-scanner -X -Dsonar.host.url=http://20.212.184.136:9000/ -Dsonar.login=${sonarLogin} -Dsonar.projectName=${env.JOB_NAME} -Dsonar.projectVersion=${env.BUILD_NUMBER} -Dsonar.projectKey=${env.JOB_BASE_NAME} -Dsonar.sources=src/main/java -Dsonar.java.libraries=target/* -Dsonar.java.binaries=target/classes -Dsonar.language=java"
        // }

        // stage("sonar quality check"){
            
        //     steps{
        //         script{
        //             withSonarQubeEnv(credentialsId: 'Sonar-token') {
        //                     sh 'chmod +x gradlew'
        //                     sh './gradlew sonarqube'
        //             }

        //             timeout(time: 1, unit: 'HOURS') {
        //               def qg = waitForQualityGate()
        //               if (qg.status != 'OK') {
        //                    error "Pipeline aborted due to quality gate failure: ${qg.status}"
        //               }
        //             }

        //         }  
        //     }
        // }       

   stage("Publish to Nexus Repository Manager") {

            steps {

                script {

                    pom = readMavenPom file: "pom.xml";

                    filesByGlob = findFiles(glob: "target/*.${pom.packaging}");

                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"

                    artifactPath = filesByGlob[0].path;

                    artifactExists = fileExists artifactPath;

                    if(artifactExists) {

                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";

                        nexusArtifactUploader(
                            nexusVersion: 'nexus3',
                            
                            protocol: 'http',

                            nexusUrl: '20.212.184.136:8081',

                            groupId: 'pom.com.mycompany.app',

                            version: 'pom.1.0-SNAPSHOT',

                            repository: 'repository/Milestone',

                            credentialsId: 'Nexus',

                            artifacts: [

                                [artifactId: 'pom.my-app',

                                classifier: '',

                                file: artifactPath,

                                type: pom.packaging],

                                [artifactId: 'pom.my-app',

                                classifier: '',

                                file: "pom.xml",

                                type: "pom"]

                            ]

                        );

                    } else {

                        error "*** File: ${artifactPath}, could not be found";

                    }

                }

            }

        }
    }

}