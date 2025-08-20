### Automation: Auto Start & Stop VM (`vm01`) in `rand-369`

We’ll use:  
- **Cloud Scheduler** → triggers **Pub/Sub** → triggers **Cloud Functions (Gen2)** → starts/stops the VM  

***

## **Step 1: Create Pub/Sub Topics**
We need two topics:  
- `start-vm-topic` → for starting the VM  
- `stop-vm-topic` → for stopping the VM  

```bash
gcloud pubsub topics create start-vm-topic --project=rand-369
gcloud pubsub topics create stop-vm-topic --project=rand-369
```

***

## **Step 2: Write Cloud Functions**

### **A. Start Function (main.py)**
```python
from googleapiclient import discovery
from google.auth import default

def start_vm(event, context):
    credentials, project = default()

    zone = "europe-north1-a"
    instance = "vm01"

    compute = discovery.build("compute", "v1", credentials=credentials)
    request = compute.instances().start(project=project, zone=zone, instance=instance)
    response = request.execute()

    print(f"✅ Started VM {instance} in zone {zone}: {response}")
```

### **B. Stop Function (main.py)**
```python
from googleapiclient import discovery
from google.auth import default

def stop_vm(event, context):
    credentials, project = default()

    zone = "europe-north1-a"
    instance = "vm01"

    compute = discovery.build("compute", "v1", credentials=credentials)
    request = compute.instances().stop(project=project, zone=zone, instance=instance)
    response = request.execute()

    print(f"🛑 Stopped VM {instance} in zone {zone}: {response}")
```

### **requirements.txt**
```
google-api-python-client
google-auth
```

***

## **Step 3: Deploy Cloud Functions (Gen2)**

### Start Function
```bash
gcloud functions deploy start-vm-function \
  --gen2 \
  --runtime=python310 \
  --region=europe-north1 \
  --entry-point=start_vm \
  --trigger-event=google.cloud.pubsub.topic.v1.messagePublished \
  --trigger-resource=start-vm-topic \
  --timeout=540s \
  --project=rand-369

```

### Stop Function
```bash
gcloud functions deploy stop-vm-function \
  --gen2 \
  --runtime=python310 \
  --region=europe-north1 \
  --entry-point=stop_vm \
  --trigger-event=google.cloud.pubsub.topic.v1.messagePublished \
  --trigger-resource=stop-vm-topic \
  --timeout=540s \
  --project=rand-369
```

***

## **Step 4: Create Cloud Scheduler Jobs**

### Start VM at **7:00 AM IST (01:30 UTC)**
```bash
gcloud scheduler jobs create pubsub start-vm-job \
  --location=europe-north1 \
  --schedule="30 1 * * *" \
  --topic=start-vm-topic \
  --message-body="Start vm01 at 7 AM IST" \
  --time-zone="UTC" \
  --project=rand-369
```
if you face any issue for above north1 region, try in west1 region
```bash
gcloud scheduler jobs create pubsub start-vm-job \
  --location=europe-west1 \
  --schedule="30 1 * * *" \
  --topic=start-vm-topic \
  --message-body="Start vm01 at 7 AM IST" \
  --time-zone="UTC" \
  --project=rand-369
```

### Stop VM at **10:00 PM IST (16:30 UTC)**
```bash
gcloud scheduler jobs create pubsub stop-vm-job \
  --schedule="30 16 * * *" \
  --topic=stop-vm-topic \
  --message-body="Stop vm01 at 10 PM IST" \
  --time-zone="UTC" \
  --project=rand-369
```

***

## **Step 5: Verify Everything**

### Check Functions
```bash
gcloud functions describe start-vm-function --region=europe-north1 --project=rand-369
gcloud functions describe stop-vm-function --region=europe-north1 --project=rand-369
```

### Check Scheduler Jobs
```bash
gcloud scheduler jobs describe start-vm-job --project=rand-369
gcloud scheduler jobs describe start-vm-job --project=rand-369 --location=europe-west
gcloud scheduler jobs describe stop-vm-job --project=rand-369
```

### Manual Testing
- Manually trigger Start:
  ```bash
  gcloud pubsub topics publish start-vm-topic --message="Manual Start Test" --project=rand-369
  ```
- Manually trigger Stop:
  ```bash
  gcloud pubsub topics publish stop-vm-topic --message="Manual Stop Test" --project=rand-369
  ```
- Verify VM status:
  ```bash
  gcloud compute instances describe vm01 \
    --zone=europe-north1-a \
    --project=rand-369 \
    --format="get(status)"
  ```

***

# ✅ Final Setup Summary
- **7:00 AM IST (01:30 UTC)** → **Cloud Scheduler** → Pub/Sub (`start-vm-topic`) → **start-vm-function** → Starts `vm01`.  
- **10:00 PM IST (16:30 UTC)** → **Cloud Scheduler** → Pub/Sub (`stop-vm-topic`) → **stop-vm-function** → Stops `vm01`.  

Everything runs on **Cloud Functions Gen 2** 👍  

***


