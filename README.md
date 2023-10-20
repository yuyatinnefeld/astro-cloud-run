# Astro + Cloud Run

## About
Astro is an all-in-one web framework for building fast, content-focused websites.

## Source
- https://docs.astro.build/en/getting-started/

## Getting Started 
### 1. Create an Astro Project
```bash
npm create astro@latest
```

### 2. Open the project and run the project
```bash
cd apparent-mercury
npm run dev
open http://localhost:4321
```

### 3. Make the project dockerized
```bash
touch Dockerfile
touch .dockerignore

IMAGE="astro-project"
docker build -t $IMAGE .
docker run -p 8080:80 $IMAGE
open http://localhost:8080
```

### 4. Deploy a Cloud Run service
```bash
# create env variables and config files
PROJECT_ID="YOUR_PROJECT_ID"
REPO_NAME="docker-repo"
REGION="europe-west1"
SERVICE_NAME="astra-service"

touch cloudbuild.yaml
touch service.yaml

# Create an Artifact Registry Repo
gcloud artifacts repositories create $REPO_NAME \
    --description="docker-repo" \
    --location=$REGION \
    --repository-format="DOCKER"

# Push the image into the Artifact Registry and 
gcloud builds submit --config=cloudbuild.yaml \
  --substitutions=_PROJECT_ID=$PROJECT_ID,_LOCATION=$REGION,_REPOSITORY=$REPO_NAME,_IMAGE="astro-image" .

# Deploy the Cloud Run service
gcloud run services replace service.yaml --region=europe-west1

# Allow public access
gcloud run services add-iam-policy-binding $SERVICE_NAME \
    --member="allUsers" \
    --role="roles/run.invoker" \
    --region=$REGION
```