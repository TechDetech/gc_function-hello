# README

## Authenticate to Google Cloud

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
        - name: Authenticate to Google Cloud
          id: 'auth'
          uses: 'google-github-actions/auth@v2'
          with:
            project_id: ticketsaver-421701
            workload_identity_provider: 'projects/123456789/locations/global/workloadIdentityPools/my-pool/providers/my-provider'
            service_account: 'my-service-account@my-project.iam.gserviceaccount.com'
```

### How to get `workload_identity_provider`

```bash
REPO="TechDetech/gc_function-hello"
BRANCH="main"
PROJECT_ID="ticketsaver-421701"
PROJECT_NUMBER=$(gcloud projects describe $PROJECT_ID --format='value(projectNumber)')
SERVICE_ID="test-wif"
IAM_POOL="github-wif-pool"

gcloud iam workload-identity-pools create "$IAM_POOL" \
    --location="global" --project "$PROJECT_ID"

gcloud iam service-accounts create "$SERVICE_ID" \
    --display-name="Service account used by WIF POC" \
    --project "$PROJECT_ID"

gcloud projects add-iam-policy-binding $PROJECT_ID \
    --member="serviceAccount:$SERVICE_ID@$PROJECT_ID.iam.gserviceaccount.com" \
    --role="roles/compute.viewer"

gcloud iam service-accounts add-iam-policy-binding "$SERVICE_ID@$PROJECT_ID.iam.gserviceaccount.com" \
    --project="$PROJECT_ID" \
    --role="roles/iam.workloadIdentityUser" \
    --member="principalSet://iam.googleapis.com/projects/$PROJECT_NUMBER/locations/global/workloadIdentityPools/$IAM_POOL/attribute.repository/$REPO" \
    --member="principal://iam.googleapis.com/projects/$PROJECT_NUMBER/locations/global/workloadIdentityPools/$IAM_POOL/subject/repo:$REPO:ref:refs/heads/$BRANCH"
```

principal://iam.googleapis.com/projects/459566627208/locations/global/workloadIdentityPools/github-wif-pool/subject/SUBJECT_ATTRIBUTE_VALUE
