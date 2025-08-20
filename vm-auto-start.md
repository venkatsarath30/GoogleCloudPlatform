setup with your **project ID: `rand-369`**.  
complete step‑by‑step automation to start **vm01** in **europe-north1-a** at **7:00 AM IST (1:30 AM UTC)** every day.

***

# 🚀 Steps to Automate VM Start (Project: `rand-369`)

***

## **Step 1: Cloud Function to Start the VM**

**File: `main.py`**
```python
from googleapiclient import discovery
from google.auth import default

def start_vm(event, context):
    # Get default credentials and project ID
    credentials, project = default()

    zone = "europe-north1-a"
    instance = "vm01"

    compute = discovery.build("compute", "v1", credentials=credentials)

    request = compute.instances().start(
        project=project,
        zone=zone,
        instance=instance
    )
    response = request.execute()

    print(f"Started VM {instance} in zone {zone}: {response}")
```

**File: `requirements.txt`**
```
google-api-python-client
google-auth
```

***

### Deploy the Function
```bash
gcloud functions deploy start-vm-function \
  --runtime python310 \
  --trigger-topic start-vm-topic \
  --timeout 540s \
  --region=europe-north1 \
  --project=rand-369
```

***

## **Step 2: Create Pub/Sub Topic**
```bash
gcloud pubsub topics create start-vm-topic \
  --project=rand-369
```

***

## **Step 3: Create Cloud Scheduler Job**
Schedule it to run **every day at 1:30 AM UTC (7:00 AM IST):**

```bash
gcloud scheduler jobs create pubsub start-vm-job \
  --schedule="30 1 * * *" \
  --topic=start-vm-topic \
  --message-body="Start vm01 at 7 AM IST" \
  --time-zone="UTC" \
  --project=rand-369
```

***

## **Step 4: Verification**

1. **Describe function**  
   ```bash
   gcloud functions describe start-vm-function --project=rand-369
   ```

2. **Describe scheduler job**  
   ```bash
   gcloud scheduler jobs describe start-vm-job --project=rand-369
   ```

3. **Test manually** (should immediately start the VM):  
   ```bash
   gcloud pubsub topics publish start-vm-topic \
     --message="Manual test" \
     --project=rand-369
   ```

4. **Check VM status:**  
   ```bash
   gcloud compute instances describe vm01 \
     --zone=europe-north1-a \
     --project=rand-369 \
     --format="get(status)"
   ```

***

✅ **Final Setup for `rand-369`:**
- **Cloud Scheduler (`start-vm-job`)** runs daily at **1:30 AM UTC ≈ 7:00 AM IST**.  
- Sends Pub/Sub message → **`start-vm-topic`**.  
- **Cloud Function (`start-vm-function`)** starts **vm01** in **europe-north1-a**.  

***
