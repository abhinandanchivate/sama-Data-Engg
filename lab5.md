## 🧪 GCP Hands-On Labs – Step-by-Step 



### **Lab 5: Deploy App on App Engine (Standard Environment)**

**Objective**: Deploy a Python Flask app using App Engine standard environment.

#### ✅ Steps:

1. **Create `main.py` for your app:**

```python
from flask import Flask
import os
app = Flask(__name__)

@app.route('/')
def home():
    return 'Hello, GCP App Engine!'

@app.route('/env')
def environment():
    return f"Running in environment: {os.getenv('ENV')}"
```

2. **Create `requirements.txt`:**

```
Flask==2.0.3
gunicorn==20.1.0
```

3. **Create environment-specific configuration files:**

* `app.dev.yaml`

```yaml
runtime: python39
entrypoint: gunicorn -b :$PORT main:app

env_variables:
  ENV: "development"
  SECRET_KEY: "dev-secret"
  DATABASE_URL: "postgres://dev_user:dev_pass@dev_host/dev_db"
```

* `app.prod.yaml`

```yaml
runtime: python39
entrypoint: gunicorn -b :$PORT main:app

env_variables:
  ENV: "production"
  SECRET_KEY: "prod-secret"
  DATABASE_URL: "postgres://prod_user:prod_pass@prod_host/prod_db"
```

4. **Deploy using a specific environment configuration:**

```bash
gcloud app deploy app.dev.yaml  # For development
# OR
gcloud app deploy app.prod.yaml  # For production
```

5. **Change the running environment (redeploy with different config):**
   If your app is already deployed using one environment file and you want to switch:

```bash
gcloud app deploy app.prod.yaml  # Switch to production
# OR
gcloud app deploy app.dev.yaml   # Switch back to development
```

* GCP App Engine will update the environment and restart the app using the new settings.

**Note**: In App Engine Standard, you **cannot change environment variables without redeploying**. Changing `env_variables` in `app.yaml` always triggers a new version deployment, which restarts the app.

---

### 🔁 Alternative: Live Environment Switching (Without App Restart)

**Use Case**: For runtime environment switching without redeploying the app.

#### ✅ Recommended Alternatives:

1. **Store environment state in Firestore/Cloud Storage/Secret Manager**
2. **Use a fetch-based method inside the app**

##### 🔧 Example using Firestore:

1. Create Firestore collection `config` with doc `env`:

```json
{
  "ENV": "production"
}
```

2. Modify `main.py`:

```python
from flask import Flask
from google.cloud import firestore

app = Flask(__name__)
db = firestore.Client()

@app.route("/env")
def get_env():
    env_doc = db.collection("config").document("env").get()
    return f"Live ENV: {env_doc.to_dict().get('ENV')}"
```

3. **Update `requirements.txt` to include Firestore:**

```
Flask==2.0.3
gunicorn==20.1.0
google-cloud-firestore==2.13.0
```

4. **Add Firestore access permissions:**
   Ensure your App Engine service account has `roles/datastore.user` permissions:

```bash
gcloud projects add-iam-policy-binding [PROJECT_ID] \
  --member=serviceAccount:[PROJECT_ID]@appspot.gserviceaccount.com \
  --role=roles/datastore.user
```

5. Deploy using a minimal `app.yaml` (no env\_variables):

```yaml
runtime: python39
entrypoint: gunicorn -b :$PORT main:app
```

6. Now you can change the `ENV` value directly in Firestore and it will be reflected live without restarting the app.

---

7. **Browse your deployed app:**

```bash
gcloud app browse
```

8. **View logs to confirm ENV:**

```bash
gcloud app logs tail -s default
```

* Navigate to `/env` endpoint in the browser to see the currently active environment.

---


