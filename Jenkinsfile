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

  stage('Code Security'){
                        parallel{
                            stage('OWASP Dependency-Check Vulnerabilities'){
                                steps{
                                      sh 'mvn dependency-check:check'
                                      dependencyCheckPublisher pattern: 'target/dependency-check-report.xml'
                                }
                            }
                            stage('Sonacube'){
                                  steps{
                                       withSonarQubeEnv('SonarQube') {
                                       sh 'mvn sonar:sonar'
                                        }
                                  }
                            }
                        }
         }

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

                            repository: 'Milestone',

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
