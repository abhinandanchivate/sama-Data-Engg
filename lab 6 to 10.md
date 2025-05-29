## 🧪 GCP Hands-On Labs – Step-by-Step 


---

### **Lab 6: Deploy App on App Engine (Flexible Environment)**

**Objective**: Deploy a Dockerized app on App Engine flexible environment for more control.

**📁 Folder Structure:**

```
app-flexible/
├── app.yaml              # App Engine configuration
├── Dockerfile            # Custom runtime definition
├── main.py               # Flask app entrypoint
├── requirements.txt      # Python dependencies
```

**📌 Folder Setup Commands:**

```bash
mkdir app-flexible && cd app-flexible
cat > main.py <<EOF
from flask import Flask
app = Flask(__name__)
@app.route('/')
def home():
    return 'Hello from App Engine Flexible!'
EOF

echo "Flask==2.0.3" > requirements.txt

cat > Dockerfile <<EOF
FROM python:3.9-slim
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
CMD [\"gunicorn\", \"-b\", \":$PORT\", \"main:app\"]
EOF

cat > app.yaml <<EOF
runtime: custom
env: flex
service: flexible-app
EOF
```

#### ✅ Steps:

1. **Create folder structure:**

```bash
mkdir app-flexible && cd app-flexible
touch Dockerfile app.yaml main.py requirements.txt
```

2. **Write `main.py` for Flask app:**

```python
from flask import Flask
app = Flask(__name__)
@app.route('/')
def home():
    return 'Hello from App Engine Flexible!'
```

3. **Add dependencies in `requirements.txt`:**

```
Flask==2.0.3
```

4. **Define the Dockerfile:**

```Dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
CMD ["gunicorn", "-b", ":$PORT", "main:app"]
```

5. **Write the `app.yaml`:**

```yaml
runtime: custom
env: flex
service: flexible-app
```

6. **Enable necessary APIs:**

```bash
gcloud services enable appengine flexibleenvironment.googleapis.com
```

7. **Deploy your flexible app:**

```bash
gcloud app deploy
```

8. **Verify by opening the app in browser:**

```bash
gcloud app browse
```

---

### **Lab 7: CI/CD with Cloud Build for App Engine**

**Objective**: Deploy app automatically to App Engine when code is pushed to GitHub.

#### ✅ Steps:

1. **Create Cloud Build configuration file `.cloudbuild.yaml`:**

```yaml
steps:
- name: 'gcr.io/cloud-builders/gcloud'
  args: ['app', 'deploy']
```

2. **Enable Cloud Build API:**

```bash
gcloud services enable cloudbuild.googleapis.com
```

3. **Connect GitHub repo:**

* Go to Cloud Console → Cloud Build → Triggers → Create Trigger
* Choose GitHub, authenticate, and select repo
* Use `main` branch and `.cloudbuild.yaml`

4. **Push your code to GitHub:**

```bash
git add .
git commit -m "Initial commit with Cloud Build"
git push origin main
```

5. **Observe auto-deployment via Cloud Build dashboard.**

---

### **Lab 8: Create Custom VPC and Subnet**

**Objective**: Launch a VM within a custom VPC and subnet.

#### ✅ Steps:

1. **Create custom VPC:**

```bash
gcloud compute networks create custom-vpc \
  --subnet-mode=custom
```

2. **Create subnet in `us-central1`:**

```bash
gcloud compute networks subnets create custom-subnet \
  --network=custom-vpc \
  --range=10.10.0.0/24 \
  --region=us-central1
```

3. **Add firewall rule for SSH and HTTP:**

```bash
gcloud compute firewall-rules create allow-ssh-http \
  --network=custom-vpc \
  --allow=tcp:22,tcp:80 \
  --source-ranges=0.0.0.0/0 \
  --target-tags=allow-access
```

4. **Create a VM using custom subnet:**

```bash
gcloud compute instances create custom-vm \
  --zone=us-central1-a \
  --machine-type=e2-medium \
  --subnet=custom-subnet \
  --tags=allow-access \
  --image-family=debian-11 \
  --image-project=debian-cloud
```

---

### **Lab 9: Enable Private Google Access**

**Objective**: Allow VM without external IP to access Google APIs.

#### ✅ Steps:

1. **Update subnet to allow Private Google Access:**

```bash
gcloud compute networks subnets update custom-subnet \
  --region=us-central1 \
  --enable-private-ip-google-access
```

2. **Create VM without public IP:**

```bash
gcloud compute instances create no-ip-vm \
  --zone=us-central1-a \
  --machine-type=e2-small \
  --subnet=custom-subnet \
  --no-address \
  --image-family=debian-11 \
  --image-project=debian-cloud
```

3. **Connect using Identity-Aware Proxy (IAP):**

```bash
gcloud compute ssh no-ip-vm --zone=us-central1-a --tunnel-through-iap
```

4. **Validate API access (e.g., metadata):**

```bash
curl -H "Metadata-Flavor: Google" http://metadata.google.internal/computeMetadata/v1/instance/id
```

---

### **Lab 10: Configure Shared VPC Across Projects**

**Objective**: Centralize networking in a host project and share it with service projects.

#### ✅ Steps:

1. **Enable Shared VPC in host project:**

```bash
gcloud compute shared-vpc enable host-project-id
```

2. **Associate service project:**

```bash
gcloud compute shared-vpc associated-projects add service-project-id \
  --host-project=host-project-id
```

3. **Grant required IAM roles:**

```bash
gcloud projects add-iam-policy-binding service-project-id \
  --member="user:you@example.com" \
  --role="roles/compute.networkUser"
```

4. **Deploy resource in service project using shared VPC subnet.**

```bash
gcloud compute instances create shared-vpc-vm \
  --project=service-project-id \
  --zone=us-central1-a \
  --machine-type=e2-medium \
  --subnet=projects/host-project-id/regions/us-central1/subnetworks/custom-subnet \
  --image-family=debian-11 \
  --image-project=debian-cloud
```

---


