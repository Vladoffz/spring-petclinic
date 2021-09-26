pipeline {
    agent any
    stages{
        stage("Build artifact"){
            steps{
             dir('spring-petclinic'){
              sh "./mvnw package"
             }
            }
        }
        stage("Delete old artifact"){
            steps{
             sh "rm *.jar"
            }
        }
        stage("Build Docker image"){
            steps{
            dir('spring-petclinic/target'){
              sh "mv *.jar /var/lib/jenkins/workspace/CIDockerpipeline"
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
                docker.withRegistry('https://gcr.io', 'gcr: gcp-admin-key'){
                    app.push("${env.BUILD_NUMBER}")
                    app.push("latest")
                }
            }
        }
