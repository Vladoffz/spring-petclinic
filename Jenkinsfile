pipeline {
    agent any
    stages{
        stage("Build artifact"){
            steps{             
              sh "./mvnw package"             
            }
        }
        stage("Delete old artifact"){
            steps{
             sh "rm *.jar"
                dir('target'){
              sh "mv *.jar /var/lib/jenkins/workspace/CD_main"
             }
            }
        }           
    }
}
node{
            checkout scm
            stage("Normal build test"){
                app = docker.build("learning-324516/petclinic")
            }
            stage("Push image"){
                docker.withRegistry('https://gcr.io', 'gcr:gcr-admin-key'){
                    app.push("${env.BUILD_NUMBER}")
                    app.push("latest")
                }
            }
        }
