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
            stage("Build image"){
                app = docker.build("learning-324516/petclinic")
            }
            stage("Push image"){
                docker.withRegistry('https://gcr.io', 'gcr:gcr-admin-key'){
                    app.push("${env.BUILD_NUMBER}")
                    app.push("latest")
                }
            }
            stage("Connect to Kubernetes cluster"){
               sh "gcloud container clusters get-credentials lab-final --region us-central1 --project learning-324516"
            }
            stage("Create deployment"){
               sh "kubectl create deploy petclinic --image=gcr.io/learning-324516/petclinic:latest --port=8080"
         }
    }
