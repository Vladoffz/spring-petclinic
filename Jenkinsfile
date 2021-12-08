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
              sh "mv *.jar /var/lib/jenkins/jobs/Petclinic-cicd"
              
             }
            }
        }           
    }
}
node{
            checkout scm
            stage("Build image"){
                app = docker.build("learning-333709/petclinic")
            }
            stage("Push image"){
                docker.withRegistry('https://gcr.io', 'gcr:gcr-admin-key'){
                    app.push("${env.BUILD_NUMBER}")
                    app.push("latest")
                }
            }
            stage("Connect to Kubernetes cluster"){
               sh "gcloud auth activate-service-account terraform@learning-333709.iam.gserviceaccount.com --key-file=./learning-333709-0a968aad1107.json"
               sh "gcloud container clusters get-credentials final-lab --region us-central1 --project learning-333709"
            }
            stage("Create Petclinic deployment"){
               sh "kubectl delete deploy petclinic"
               sh "kubectl create deploy petclinic --image=gcr.io/learning-333709/petclinic:latest --port=8080"
           }
           stage("Connect deployment LoadBalancer"){
               sh "kubectl delete svc petclinic"
               sh "kubectl expose deploy petclinic --port=8080 --type=LoadBalancer"
           }
    }
