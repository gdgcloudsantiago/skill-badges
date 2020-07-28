# Perform Foundational Infrastructure Tasks in Google Cloud: Challenge Lab

▶️ 
[YouTube - Next OnAir Extended - Skill Badges Challenge - Perform Foundational Infrastructure Tasks in Google Cloud
](https://youtu.be/ELBlxPNkGjI)

Abrir Cloud Shell o utiliza Google SDK para aplicar estos comandos:

## CONFIGURACIONES ANTES DE EMPEZAR:

Configuración de la región y zona por defecto para los comandos gcloud según laboratorio us-east1-x

```
gcloud config set compute/region us-east1
gcloud config set compute/zone us-east1-b
```

## CREACIÓN DEL BUCKET DE CLOUD STORAGE:

Creación del bucket:

```
gsutil mb -b on -l us-east1 gs://REEMPLAZAR_POR_ID_DEL_PROYECTO
```
✅CHECK01

## CREACIÓN DEL TÓPICO DE PUB/SUB:

Creación del tópico:

```
gcloud pubsub topics create my-topic
```
✅CHECK02

## CREACIÓN DE LA CLOUD FUNCTION:

Crear Cloud Function en la sección Cloud Function -> Create Function con la siguiente configuración: 

A) Name: function-1

B) Memory Allocated: 256 MiB

C) Trigger : Cloud Storage (Seleccionar Cloud Storage creado en el punto 1)

D) Copiar index.js y package.json que aparece en el laboratorio de qwiklabs (Reemplazar donde dice "REPLACE_WITH_YOUR_TOPIC" en index.js por my-topic y reemplazar ) 

E) Function to Execute: thumbnail

F) Click en Create

Luego de creada la Cloud Function con las configuraciones especificadas, cargar el archivo indicado en el laboratorio en el bucket creado en el punto 1.

✅CHECK03

## REMOVER PERMISOS:

```
gcloud projects remove-iam-policy-binding <project-ID>\
--member=user:<user-2> --role=roles/viewer
```

✅CHECK04

