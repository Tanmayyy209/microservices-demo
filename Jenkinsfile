pipeline {

 agent any

 environment {
  PROJECT_ID = "project-7c9e70a5-5331-440c-8ed"
  REGION = "us-central1"
  REPO = "boutique-repo"
 }

 stages {

 stage('Clone Repo') {
  steps {
   git 'https://github.com/GoogleCloudPlatform/microservices-demo.git'
  }
 }

 stage('Set GCP Config') {
  steps {
   sh '''
   gcloud config set project $PROJECT_ID
   gcloud config set auth/impersonate_service_account jenkins-sa@$PROJECT_ID.iam.gserviceaccount.com
   '''
  }
 }

 stage('Connect to GKE') {
  steps {
   sh '''
   gcloud container clusters get-credentials online-boutique --region $REGION
   '''
  }
 }

 stage('Build and Push Images') {
  steps {
   script {

    def services = [
     "frontend",
     "cartservice",
     "checkoutservice",
     "currencyservice",
     "emailservice",
     "paymentservice",
     "productcatalogservice",
     "recommendationservice",
     "shippingservice",
     "adservice"
    ]

    for (service in services) {

     sh """
     docker build -t $REGION-docker.pkg.dev/$PROJECT_ID/$REPO/${service}:latest ./src/${service}

     docker push $REGION-docker.pkg.dev/$PROJECT_ID/$REPO/${service}:latest
     """

    }

   }
  }
 }

 stage('Deploy to Kubernetes') {
  steps {
   sh '''
   kubectl apply -f release/kubernetes-manifests.yaml
   '''
  }
 }

 }

}
