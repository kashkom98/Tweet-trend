def registry = 'https://kashyap.jfrog.io'
def imageName = 'kashyap.jfrog.io/valaxy-docker-local/ttrend'
def version   = '2.1.3'

pipeline {
    agent {
        node {
            label "maven"
        }
    }


environment {
    PATH = "/opt/apache-maven-3.9.6/bin:$PATH"
}
    stages {
        stage('build') {
            steps {
                // echo 'Hello World'
                sh 'mvn clean deploy'
            }
        }

        // stage('SonarQube analysis') {
        // environment {
        //     scannerHome = tool 'sonar-scanner'
        // }
        // steps {
        // withSonarQubeEnv('sonarqube-server') { // If you have configured more than one global server connection, you can specify its name
        //     sh "${scannerHome}/bin/sonar-scanner"
        // }   
        // }
        // }
        // Jfrog artifactory
            //https://kashyap.jfrog.io/

        //Artifact location  
            // /home/ubuntu/jenkins/workspace/ttrend-multibranch_main/jarstaging/com/valaxy/demo-workshop/2.1.2
    
        //Credentials
            // artifact-cred

        stage("Jar Publish") {
        steps {
            script {
                    echo '<--------------- Jar Publish Started --------------->'
                     def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"artifact-cred"
                     def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                     def uploadSpec = """{
                          "files": [
                            {
                              "pattern": "jarstaging/(*)",
                              "target": "maven-libs-release-local/{1}",
                              "flat": "false",
                              "props" : "${properties}",
                              "exclusions": [ "*.sha1", "*.md5"]
                            }
                         ]
                     }"""
                     def buildInfo = server.upload(uploadSpec)
                     buildInfo.env.collect()
                     server.publishBuildInfo(buildInfo)
                     echo '<--------------- Jar Publish Ended --------------->'  
            
            }
        }   
    }


        stage(" Docker Build ") {
        steps {
            script {
                echo '<--------------- Docker Build Started --------------->'
                    app = docker.build(imageName+":"+version)
                echo '<--------------- Docker Build Ends --------------->'
            }
        }
        }

        stage (" Docker Publish "){
        steps {
            script {
               echo '<--------------- Docker Publish Started --------------->'  
                docker.withRegistry(registry, 'artifact-cred'){
                    app.push()
                }    
               echo '<--------------- Docker Publish Ended --------------->'  
            }
        }
        }

        // stage ("Deploy"){
        //     steps {
        //         script {
        //             sh './deploy.sh'
        //         }
        //     }
        // }

       stage(" Deploy ") {
       steps {
         script {
            echo '<--------------- Helm Deploy Started --------------->'
            sh 'helm install ttrend ttrend-0.1.0.tgz'
            echo '<--------------- Helm deploy Ends --------------->'
         }
       }
     }

    }
}