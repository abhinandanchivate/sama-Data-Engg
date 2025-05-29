## 🧪 GCP Hands-On Labs – Step-by-Step


---

### **Lab 11: HTTP(S) Load Balancer with Managed Instance Group**

**Objective**: Create a global external HTTP Load Balancer that distributes traffic across VM instances.

#### ✅ Steps:

1. **Create instance template with startup script:**

```bash
gcloud compute instance-templates create web-template \
  --machine-type=e2-micro \
  --image-family=debian-11 \
  --image-project=debian-cloud \
  --tags=http-server \
  --metadata=startup-script='#! /bin/bash
sudo apt update
sudo apt install -y apache2
sudo systemctl start apache2
echo "$(hostname)" | sudo tee /var/www/html/index.html'
```

2. **Create a Managed Instance Group (MIG):**

```bash
gcloud compute instance-groups managed create web-mig \
  --base-instance-name=web \
  --size=2 \
  --template=web-template \
  --zone=us-central1-a
```

3. **Create a health check:**

```bash
gcloud compute health-checks create http basic-check --port 80
```

4. **Create backend service and attach MIG:**

```bash
gcloud compute backend-services create web-backend \
  --protocol=HTTP \
  --health-checks=basic-check \
  --global

gcloud compute backend-services add-backend web-backend \
  --instance-group=web-mig \
  --instance-group-zone=us-central1-a \
  --global
```

5. **Create URL map and HTTP proxy:**

```bash
gcloud compute url-maps create web-map --default-service=web-backend

gcloud compute target-http-proxies create http-lb-proxy --url-map=web-map
```

6. **Reserve global IP and create forwarding rule:**

```bash
gcloud compute addresses create web-ip --ip-version=IPV4 --global

gcloud compute forwarding-rules create http-lb \
  --address=web-ip \
  --global \
  --target-http-proxy=http-lb-proxy \
  --ports=80
```

7. **Access Load Balancer in browser:**

```bash
gcloud compute addresses describe web-ip --global
```

---

### **Lab 12: Enable and Configure CDN for Load Balancer**

**Objective**: Boost static content delivery performance by enabling Cloud CDN.

#### ✅ Steps:

1. **Enable CDN on backend service:**

```bash
gcloud compute backend-services update web-backend \
  --enable-cdn \
  --global
```

2. **Verify CDN headers:**

```bash
curl -I http://[LB_STATIC_IP]
# Check for: X-Cache: HIT or MISS
```

---

### **Lab 13: Load Balance App Engine Using HTTP(S) LB**

**Objective**: Use Load Balancer to serve traffic from App Engine application.

#### ✅ Steps:

1. **Deploy App Engine app:**

```bash
gcloud app deploy
```

2. **Create backend service for App Engine:**

```bash
gcloud compute backend-services create appengine-backend \
  --protocol=HTTP \
  --app-engine \
  --global
```

3. **Create URL map and target proxy:**

```bash
gcloud compute url-maps create appengine-map --default-service=appengine-backend

gcloud compute target-http-proxies create appengine-proxy --url-map=appengine-map
```

4. **Create global IP and forwarding rule:**

```bash
gcloud compute addresses create appengine-ip --global

gcloud compute forwarding-rules create appengine-rule \
  --address=appengine-ip \
  --global \
  --target-http-proxy=appengine-proxy \
  --ports=80
```

5. **Test the load balancer with App Engine backend.**

---

### **Lab 14: Map Custom Domain Using Cloud DNS**

**Objective**: Configure a domain (e.g., `example.com`) to point to Load Balancer.

#### ✅ Steps:

1. **Create a managed DNS zone:**

```bash
gcloud dns managed-zones create my-zone \
  --dns-name="example.com." \
  --description="My custom domain"
```

2. **Add A record for Load Balancer IP:**

```bash
gcloud dns record-sets transaction start --zone=my-zone

gcloud dns record-sets transaction add --zone=my-zone \
  --name="www.example.com." \
  --ttl=300 \
  --type=A \
  [LB_STATIC_IP]

gcloud dns record-sets transaction execute --zone=my-zone
```

3. **Update registrar’s name servers to use Google Cloud DNS.**

---

### **Lab 15: IAM and Firewall Rules for Secure Access**

...(unchanged content here)

---

### **Lab 16: Shared VPC Configuration**

**Objective**: Set up a Shared VPC to allow multiple service projects to share a centralized network managed by a host project, enabling resource isolation and centralized control across multiple GCP projects.

#### ✅ Steps:

1. **Designate a host project:**

```bash
gcloud compute shared-vpc enable [HOST_PROJECT_ID]
```

2. **Associate multiple service projects with the host VPC:**

```bash
gcloud compute shared-vpc associated-projects add [SERVICE_PROJECT_ID_1] \
  --host-project=[HOST_PROJECT_ID]

gcloud compute shared-vpc associated-projects add [SERVICE_PROJECT_ID_2] \
  --host-project=[HOST_PROJECT_ID]
```

3. **Grant required roles to users or service accounts in each service project:**

```bash
gcloud projects add-iam-policy-binding [HOST_PROJECT_ID] \
  --member="user:[USER_EMAIL_1]" \
  --role="roles/compute.networkUser"

gcloud projects add-iam-policy-binding [HOST_PROJECT_ID] \
  --member="user:[USER_EMAIL_2]" \
  --role="roles/compute.networkUser"
```

4. **Create a shared subnet in the host project:**

```bash
gcloud compute networks subnets create shared-subnet \
  --project=[HOST_PROJECT_ID] \
  --network=default \
  --region=us-central1 \
  --range=10.100.0.0/24
```

5. **Deploy VMs in both service projects using the host project’s subnet:**

```bash
gcloud compute instances create service1-vm \
  --project=[SERVICE_PROJECT_ID_1] \
  --zone=us-central1-a \
  --machine-type=e2-medium \
  --subnet=projects/[HOST_PROJECT_ID]/regions/us-central1/subnetworks/shared-subnet \
  --image-family=debian-11 \
  --image-project=debian-cloud


gcloud compute instances create service2-vm \
  --project=[SERVICE_PROJECT_ID_2] \
  --zone=us-central1-a \
  --machine-type=e2-medium \
  --subnet=projects/[HOST_PROJECT_ID]/regions/us-central1/subnetworks/shared-subnet \
  --image-family=debian-11 \
  --image-project=debian-cloud
```

6. **✅ Verify shared VPC usage:**

* Navigate to Compute Engine → VM Instances in each service project and confirm the VMs are using the shared subnet from the host project.
* In the host project, go to VPC → Firewall and confirm firewall rules are affecting both VMs.
* Use SSH or ping tests to verify network access control and firewall enforcement.

---


