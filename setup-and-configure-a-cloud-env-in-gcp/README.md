Set up and Configure a Cloud Environment in Google Cloud: Challenge Lab

#CONFIGURACIONES ANTES DE EMPEZAR:

#Configuración de la región y zona por defecto para los comandos gcloud según laboratorio us-east1-x 
gcloud config set compute/region us-east1 
gcloud config set compute/zone us-east1-b

#CREACIÓN DE VPC griffin-dev-vpc

gcloud compute networks create griffin-dev-vpc --subnet-mode custom

#Creacion de subnetworks dentro de vpc

gcloud compute networks subnets create griffin-dev-wp --network=griffin-dev-vpc --region=us-east1 --range=192.168.16.0/20
gcloud compute networks subnets create griffin-dev-mgmt --network=griffin-dev-vpc --region=us-east1 --range=192.168.32.0/20

#CHECK01

#Copiar template de Deployment Manager desde bucket 

gsutil cp -r gs://cloud-training/gsp321/dm .

#Setear region us-east1 en template prod-network.yaml

#Deploy de VPC prod en deployment manager

gcloud deployment-manager deployments create griffin-prod-vpc --config prod-network.yaml

#CHECK02

#Crear bastion host con dos network interfaces a las redes griffin-dev-mgmt y griffin-prod-mgmt

gcloud compute instances create griffin-bastion --machine-type=n1-standard-1 --network-interface subnet=griffin-dev-mgmt --network-interface subnet=griffin-prod-mgmt --tags bastion 

#Crear regla de FW para acceder via SSH

gcloud compute firewall-rules create griffin-dev-mgmt-ssh-bastion --direction=INGRESS --priority=1000 --network=griffin-dev-vpc --action=ALLOW --rules=tcp:22 --source-ranges=0.0.0.0/0 --source-tags bastion
gcloud compute firewall-rules create griffin-prod-mgmt-ssh-bastion --direction=INGRESS --priority=1000 --network=griffin-prod-vpc --action=ALLOW --rules=tcp:22 --source-ranges=0.0.0.0/0 --source-tags bastion

#CHECK03

#Crear instancia de CloudSQL griffin-dev-db

gcloud sql instances create griffin-dev-db --tier db-n1-standard-1 --region=us-east1 

#Setear password de usuario root de MySQL

gcloud sql users set-password root --host=% --instance=griffin-dev-db --password gdgsantiago

#Conectarse a la instancia SQL y ejecutar los comandos mencionados (MySQL Client-Cloud Shell)

gcloud sql connect griffin-dev-db 

CREATE DATABASE wordpress;
GRANT ALL PRIVILEGES ON wordpress.* TO "wp_user"@"%" IDENTIFIED BY "stormwind_rules";
FLUSH PRIVILEGES;

#CHECK04

#Crear Cluster Kubernetes

gcloud container clusters create griffin-dev --zone=us-east1-b --machine-type=n1-standard-4 --num-nodes=2 --network=griffin-dev-vpc --subnetwork griffin-dev-wp

#CHECK05

#Copiar archivos para desplegar wordpress en kubernetes desde bucket

gsutil cp -r gs://cloud-training/gsp321/wp-k8s .

#Modificar credenciales en archivo wp-env-yaml (wp_user-stormwind_rules)

#Creacion de json cuenta de servicio y de secret para conexion a DB 

gcloud iam service-accounts keys create key.json --iam-account=cloud-sql-proxy@$GOOGLE_CLOUD_PROJECT.iam.gserviceaccount.com

kubectl apply -f wp-env.yaml

kubectl create secret generic cloudsql-instance-credentials --from-file key.json

#CHECK06

#Editar wp-deployment.yaml para agregar instance connection name de la db (tipo qwiklabs-gcp-00-12cb5662d785:us-east1:griffin-dev-db)

#Crear deployment y service en GKE

kubectl apply -f wp-deployment.yaml
kubectl apply -f wp-service.yaml

#Ver IP LB y probar acceso a wp

#CHECK07

#Crear uptime ckeck para wp site

Ir a Monitoring --> Uptime checks --> create uptime check
Title : griffin-uptime-check
Target: URL
Hostname - path

#CHECK08

#Dar acceso a segundo user al project with an Editor Role

gcloud projects add-iam-policy-binding <project-ID> --member=user:<user-2> --role=roles/editor

#CHECK09


