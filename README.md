# 🚀 Jenkins CI/CD Pipeline Setup — Manual & Automated

This guide walks through installing **Jenkins**, creating jobs, connecting them into a **manual pipeline**, and then **automating** the entire CI/CD workflow using **GitHub webhooks**.


## 🧩 Introduction

**Jenkins** is an open-source automation server that helps implement **Continuous Integration (CI)** and **Continuous Deployment (CD)** for software projects.

This project demonstrates:
- Installing Jenkins on a Linux server
- Creating and linking build/test/deploy jobs
- Building a manual pipeline
- Automating job triggers using GitHub webhooks

---

## 🧱 Prerequisites
Before starting, ensure you have:
* AWS Account with EC2 access
* Ubuntu 22.04 LTS (t2.micro recommended for demo)
* Inbound port **22 (SSH)** and **8080 (Jenkins)** open in the security group
* Basic understanding of Linux commands
* GitHub repository for integration
---

## 3. Launch & Configure EC2 Instance

1. Go to **AWS EC2 Console** → **Launch Instance**
2. Choose:

   * **AMI:** Ubuntu Server 22.04 LTS
   * **Instance Type:** t2.micro
   * **Key Pair:** Create or select existing
   * **Security Group:** Allow SSH (22) and Custom TCP (8080)
3. Launch the instance and note down its **Public IP**.

<img width="602" height="303" alt="image" src="https://github.com/user-attachments/assets/58d2496b-4400-40ec-be6e-b67c23d663f5" />

<img width="602" height="191" alt="image" src="https://github.com/user-attachments/assets/7dc91071-d04c-47af-b765-25b0cdb162d1" />

<img width="602" height="269" alt="image" src="https://github.com/user-attachments/assets/91f5112d-d5a1-4a4f-b6c9-dfa786eb1b37" />


<img width="602" height="262" alt="image" src="https://github.com/user-attachments/assets/7c0520fe-42d3-467e-8bd3-ea47179611ce" />


---

## 4. SSH into EC2 & Set Hostname

```bash
ssh -i <your-key.pem> ubuntu@<public-ip>

# Set hostname
sudo hostnamectl set-hostname Jenkins-lab
sudo init 6
```

This restarts the server with the new hostname.

<img width="602" height="48" alt="image" src="https://github.com/user-attachments/assets/446446da-b371-4ca2-82f0-4874867e82c1" />


---

## 5. Update System & Install Java

Jenkins requires Java (JRE). Install and verify:

```bash
sudo apt update
sudo apt install fontconfig openjdk-21-jre -y
java -version
```
<img width="602" height="70" alt="image" src="https://github.com/user-attachments/assets/ef7190d0-2929-45a2-bcb8-7bcac83aad96" />

---

## 6. Install Jenkins (LTS)

The Long-Term Support (LTS) release is stable and production-ready.

```bash
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key

echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/" | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt update
sudo apt install jenkins -y
```

---

## 7. Start & Enable Jenkins Service

```bash
sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins
```

Ensure the status shows **active (running)**.

<img width="602" height="276" alt="image" src="https://github.com/user-attachments/assets/7c130635-b324-4c4f-843b-d3356ba13127" />

---

## 8. Access Jenkins from Browser

Use your instance’s **public IP** and port **8080**:

```
http://<public-ip>:8080/
```

Example:

```
http://54.227.31.251:8080/
```

<img width="602" height="291" alt="image" src="https://github.com/user-attachments/assets/0574b92a-d621-4b6a-b401-aa5b4a57e8e0" />

---

## 9. Unlock Jenkins & Initial Setup

Retrieve the initial admin password:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Copy the password and paste it into the Jenkins unlock page.

<img width="602" height="38" alt="image" src="https://github.com/user-attachments/assets/5f82953f-813c-4547-ac10-85e8c6258873" />

---

## 10. Install Suggested Plugins

Select **“Install suggested plugins”** when prompted. Jenkins will automatically download commonly used plugins like:

* Git
* Pipeline
* Credentials Binding
* SSH Agent
* Build Timeout

<img width="602" height="315" alt="image" src="https://github.com/user-attachments/assets/56df62d8-9be9-47a5-9214-c3a869651577" />

<img width="602" height="277" alt="image" src="https://github.com/user-attachments/assets/46e872c1-e00c-4f2d-abda-f57cef0f4527" />

---

## 11. Create First Admin User

After plugin installation:

* Enter username, password, full name, and email.
* Click **Save and Continue**.

Then select **“Start using Jenkins”**.

<img width="602" height="355" alt="image" src="https://github.com/user-attachments/assets/53e41494-3ee0-4875-9ddf-95bac1031db0" />

<img width="602" height="355" alt="image" src="https://github.com/user-attachments/assets/72d91ace-beb3-49a5-8242-ccfc7270b43a" />

<img width="602" height="168" alt="image" src="https://github.com/user-attachments/assets/dc48b64f-f78c-4443-a777-f1e477525b40" />

You will see the Jenkind Dashboard

<img width="602" height="290" alt="image" src="https://github.com/user-attachments/assets/f2a5abed-27b7-4a4b-8b4c-6a1224be7fee" />

<img width="602" height="284" alt="image" src="https://github.com/user-attachments/assets/5d33ca3c-a8ab-425a-b02f-e52d2137c172" />

---

## 🧩 Step 3: Creating Jenkins Jobs

We’ll create multiple jobs and later link them into a pipeline.

### 🔹 1. Create `clone-job`

* Go to **New Item → Freestyle project → clone-job**
* Configure Git repository
* Save and **Build Now**

<img width="602" height="291" alt="image" src="https://github.com/user-attachments/assets/f76dbdf8-386f-4a8e-87e5-c6fc4197968a" />

<img width="602" height="152" alt="image" src="https://github.com/user-attachments/assets/3ad25d09-7ed8-43a0-9845-bb61aa23acbc" />

<img width="602" height="188" alt="image" src="https://github.com/user-attachments/assets/7f0fa51e-5408-4972-9498-dd055e48f7d3" />

<img width="602" height="171" alt="image" src="https://github.com/user-attachments/assets/892fc603-fd8b-4e54-b032-c0a505172f19" />


### 🔹 2. Create `test-job`

* Create a new freestyle project named **test-job**
* Add test execution commands under **Build → Execute Shell**
* Build the job → ensure success ✅

<img width="602" height="283" alt="image" src="https://github.com/user-attachments/assets/1468fab1-3118-4a57-a89b-5d809fd40e3f" />

<img width="602" height="180" alt="image" src="https://github.com/user-attachments/assets/9902ac8c-425c-4d72-b36d-f8ff9ba95c0c" />

<img width="602" height="273" alt="image" src="https://github.com/user-attachments/assets/52367592-1ba6-4070-ba01-1c5671b04a00" />



### 🔹 3. Create `build-job`

* Copy the configuration from **clone-job**
* Modify build steps as required
* Build and verify

<img width="602" height="187" alt="image" src="https://github.com/user-attachments/assets/3602b9c6-a790-4664-9922-4789b0f84b31" />

<img width="602" height="101" alt="image" src="https://github.com/user-attachments/assets/b25a66f8-2d09-454b-9a40-839f258f2458" />

<img width="602" height="89" alt="image" src="https://github.com/user-attachments/assets/9d59464c-ebb2-43d6-8427-9af295b4f1ba" />

<img width="602" height="235" alt="image" src="https://github.com/user-attachments/assets/48b4b892-5c52-4ab3-a5f2-0b3198c7ee75" />



### 🔹 4. Create `deploy-job`

* Again, copy from **clone-job**
* Adjust deployment script as needed
* Build to confirm success

<img width="602" height="155" alt="image" src="https://github.com/user-attachments/assets/5a3fc8d5-ac8f-4889-be47-213f8e7642f0" />

<img width="602" height="156" alt="image" src="https://github.com/user-attachments/assets/8472c89e-7a55-4745-a67e-e06b26d33117" />

<img width="602" height="148" alt="image" src="https://github.com/user-attachments/assets/95adae5c-5704-46b7-8974-b90417b8eaba" />

<img width="602" height="285" alt="image" src="https://github.com/user-attachments/assets/de807c99-85de-43c6-b9df-f1a6ed245ccc" />


At this point, you have four individual jobs ready.

<img width="602" height="206" alt="image" src="https://github.com/user-attachments/assets/d1545e7b-aa03-43b6-8c10-cac108744696" />

---

## 🔗 Step 4: Building a Manual Pipeline

We’ll now connect all jobs into a **Build Pipeline View**.

### 🔹 Install Required Plugins

Go to:

```
Manage Jenkins → Plugins → Available Plugins
```

Install:

* **Build Pipeline Plugin**
* **Pipeline View Plugin**

<img width="602" height="187" alt="image" src="https://github.com/user-attachments/assets/211662a7-b9f6-4ca5-bcf4-2b9e52a22313" />


<img width="602" height="59" alt="image" src="https://github.com/user-attachments/assets/e21c0b4d-b5b2-4c4a-9983-dbb1046331d0" />


<img width="602" height="63" alt="image" src="https://github.com/user-attachments/assets/65bcb77f-7b5c-469c-b62a-804e38c8c980" />

<img width="602" height="219" alt="image" src="https://github.com/user-attachments/assets/d6686d53-04fc-40dc-84d3-eeb6a64f7c04" />


---

### 🔹 Create a New Pipeline View

1. Go to **New View → Pipeline View**
2. Name it **1st-pipeline**
3. Select **Build Pipeline View**
4. Choose **clone-job** as the first job
5. Set “Number of builds to display” = 5
6. Save

<img width="602" height="246" alt="image" src="https://github.com/user-attachments/assets/f1db1c28-06c2-40f3-be0e-b9f801ab8100" />

<img width="602" height="76" alt="image" src="https://github.com/user-attachments/assets/30f26543-b80e-48ea-b905-5f85053ff9d8" />

<img width="602" height="80" alt="image" src="https://github.com/user-attachments/assets/ca8b91ec-5bc4-4401-b396-81a9373bff91" />



Now you’ll see the **pipeline view** in the Jenkins dashboard.


<img width="602" height="185" alt="image" src="https://github.com/user-attachments/assets/29223fcb-5a54-47f6-bab8-da535032c0cd" />

---

### 🔹 Configure Job Dependencies

#### Clone-Job → Test-Job

Go to **clone-job → Configure → Post-build Actions**

* Add “Build Other Projects”
* Choose **test-job**
* Select “Trigger only if build is stable”

<img width="602" height="295" alt="image" src="https://github.com/user-attachments/assets/68db913f-6e52-4211-9246-fdc7c9bddb7f" />


<img width="602" height="177" alt="image" src="https://github.com/user-attachments/assets/0ee3b834-4f67-4334-9b72-10a44ac58287" />


#### Test-Job → Build-Job

In **test-job → Configure → Build Triggers**

* Enable “Build after other projects are built”
* Enter **clone-job** under “Projects to watch”

<img width="602" height="173" alt="image" src="https://github.com/user-attachments/assets/6d8c3ae4-a754-4a3b-b126-facc554fe2d2" />



Add post-build action:

* “Build other projects”
* Select **build-job**

<img width="602" height="185" alt="image" src="https://github.com/user-attachments/assets/9e44ea81-1e38-4c5a-90ad-4cfe57a228fe" />

<img width="602" height="202" alt="image" src="https://github.com/user-attachments/assets/40b3335f-956f-41f5-8000-191ee1283ef6" />




#### Build-Job → Deploy-Job

In **build-job → Configure → Post-build Actions**

* Trigger **deploy-job**
* Save changes

Now all jobs are linked in sequence.
<img width="602" height="223" alt="image" src="https://github.com/user-attachments/assets/b68ae2c3-809d-4b75-b531-e2d87c761bff" />

<img width="602" height="230" alt="image" src="https://github.com/user-attachments/assets/5c0db571-2add-435f-b18b-6558f3854618" />

<img width="602" height="215" alt="image" src="https://github.com/user-attachments/assets/4a01d1ed-3125-4ebd-8e21-cda341ec070b" />

#### in Deploy-Job

Now go to deploy-job and add just only jobs to watch and it does not have any next jobs,
Save it.

<img width="602" height="265" alt="image" src="https://github.com/user-attachments/assets/873304cf-e33f-4a58-aab9-c817facc9880" />

If you have already executed the job manually, make sure to delete the previously cloned repository, or do the following in the Clone-Job configuration:
Go to Build Environment, and enable the option “Delete workspace before build starts” as shown below.

<img width="602" height="220" alt="image" src="https://github.com/user-attachments/assets/008f3b1b-1788-4233-8304-09a7936253d7" />

---

### ▶️ Run the Manual Pipeline

* Go to your pipeline view
* Click **Run** (starting with `clone-job`)
* The subsequent jobs will auto-trigger
* Observe successful pipeline execution ✅


<img width="602" height="220" alt="image" src="https://github.com/user-attachments/assets/e9f5cabc-a9a3-4b8c-b07e-da71753bc2ff" />

---

## 🤖 Step 5: Automating with GitHub Webhooks

We’ll now automate the process so Jenkins triggers builds automatically when code changes in GitHub.

### 🔹 Install Plugins

Install the following plugins:

* **GitHub Integration Plugin**
* **Generic Webhook Trigger Plugin**


<img width="602" height="62" alt="image" src="https://github.com/user-attachments/assets/8bf1e5b5-14dc-4b74-88dd-7baf06de11bb" />

<img width="602" height="76" alt="image" src="https://github.com/user-attachments/assets/469aa698-e742-4570-8b13-095fe8cb760f" />


---

### 🔹 Configure Clone Job for Automation

In **clone-job → Configure → Build Triggers**

* Check **GitHub hook trigger for GITScm polling**
* Save


<img width="602" height="157" alt="image" src="https://github.com/user-attachments/assets/04c5a7da-2ba9-4b9f-8b2a-aca4e30e8279" />

---

### 🔹 Configure Webhook in GitHub

1. Go to your **GitHub Repository → Settings → Webhooks**
2. Click **Add Webhook**
3. **Payload URL** = `http://<jenkins-public-ip>:8080/github-webhook/`
4. **Content Type** = `application/json`
5. Disable **SSL verification**
6. Click **Add Webhook**


<img width="602" height="177" alt="image" src="https://github.com/user-attachments/assets/3e2e0c5a-70a5-4944-96b9-42cc91eecc59" />

<img width="602" height="298" alt="image" src="https://github.com/user-attachments/assets/6d6ee5b9-49cd-4ab7-825b-38a53cc187f5" />


<img width="602" height="140" alt="image" src="https://github.com/user-attachments/assets/8a1f8a68-6538-486e-98cc-6c286fdbb407" />


---

### 🔹 Test the Automation.

* Make a small change in the repository (e.g., update version in `pom.xml`)
* Commit and push the change
* Jenkins will automatically trigger the pipeline build 🚀

<img width="602" height="77" alt="image" src="https://github.com/user-attachments/assets/ee51e840-1cc0-412f-a8ef-40e76dd8f41f" />


<img width="602" height="227" alt="image" src="https://github.com/user-attachments/assets/b11c7484-78d4-48d6-a9ab-c3d10009e54c" />

<img width="602" height="229" alt="image" src="https://github.com/user-attachments/assets/3d9a8fa4-12d6-424d-a082-d71415a8e533" />


---

## 🧠 Summary

| Stage                | Description                                        |
| -------------------- | -------------------------------------------------- |
| Jenkins Installation | Installed and configured Jenkins LTS               |
| Jenkins Setup        | Unlocked Jenkins, installed plugins, created admin |
| Job Creation         | Created clone, test, build, deploy jobs            |
| Manual Pipeline      | Linked jobs using build triggers                   |
| Automation           | Integrated Jenkins with GitHub webhooks            |

---

## 🧩 Why This Project Matters

This project demonstrates the **end-to-end CI/CD process**:

* Manual → Automated transition
* Multi-job coordination
* GitHub integration
* Real-time build triggering

It’s ideal for DevOps learners or interview demos to showcase **pipeline orchestration** and **automation skills**.
