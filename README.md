### **How to Run an AAP on Azure Firewall Diagnostic Playbook**

To ensure our SRE team can successfully configure, monitor, and maintain your Ansible Automation Platform (AAP) on Azure instance, your firewall must allow outbound connections to several key subdomains.

If you are experiencing connectivity issues, this diagnostic playbook will test these connections from your AAP instance.

* A **Successful** job means your firewall rules are correct, and no action is needed.
* A **Failed** job means one or more required domains are blocked, and you will need to adjust your firewall rules.

---

### **Prerequisites**

Before you begin, please ensure you have the following information:

1.  **Repository URL:** `https://github.com/ansible-content-lab/azure-firewall-policy`
2.  **Playbook Name:** `playbooks/main.yml`
3.  **Permissions:** Administrative access to your AAP Controller UI.

---

### **Step 1: Create a New Project**

First, you need to create a Project in AAP to pull the diagnostic playbook from the Git repository.

1.  Log in to your Ansible Automation Platform Controller.
2.  In the left-hand navigation menu, go to **Resources** > **Projects**.
3.  Click the **Add** button.
4.  Fill in the form:
    * **Name:** `AAP Firewall Diagnostic`
    * **Organization:** Select your default organization.
    * **Source Control Type:** Select `Git`.
    * **Source Control URL:** `https://github.com/rraafffaa/AAP_Azure_Firewall`
    * **Source Control Branch/Tag/Ref:** Leave as-is (it will default to `main`).
    * **Source Control Credential:** Leave blank; this repository is public.
5.  Click **Save**.
6.  After saving, find your new `AAP Firewall Diagnostic` project in the list and click the **Sync** icon (circular arrows) to download the project files. Wait for the sync to complete successfully.

---

### **Step 2: Create a Job Template**

Next, create a Job Template to define *how* to run the playbook. This job will run against `localhost` (the AAP instance itself) to test its own outbound connections.

1.  In the left-hand navigation menu, go to **Resources** > **Templates**.
2.  Click the **Add** button and select **Add Job Template**.
3.  Fill in the form:
    * **Name:** `Run Firewall Check`
    * **Job Type:** `Run`
    * **Inventory:** Select `Demo Inventory` (this inventory includes `localhost`).
    * **Project:** Select the `AAP Firewall Diagnostic` project you created in Step 1.
    * **Playbook:** Select `playbooks/main.yml` from the dropdown.
    * **Limit:** Type `localhost` into this field. This is **critical** as it ensures the job runs on the AAP instance itself.
    * **Credentials:** No credentials are required for this check.
4.  Click **Save**.

---

### **Step 3: Launch the Job**

Now you are ready to run the diagnostic test.

1.  Go back to the **Templates** page.
2.  Find the `Run Firewall Check` template you just created.
3.  Click the **Launch** icon (rocket ship) to run the job.

---

### **Step 4: Analyze the Results**

You will be automatically redirected to the **Jobs** output screen, where you can watch the playbook run in real-time.

#### **✅ If the Job is SUCCESSFUL**

* **What you'll see:** The job status will show `Successful` with a green bar.
* **Meaning:** Congratulations! Your AAP instance can successfully connect to all required domains.
* **Next Steps:** No further action is required.

#### **❌ If the Job is FAILED**

* **What you'll see:** The job status will show `Failed` with a red bar.
* **Meaning:** This indicates that your firewall or network security rules are blocking one or more of the required subdomains.
* **Next Steps:**
    1.  Review the job output log on the screen. The playbook is designed to show *which* domain(s) failed the connection test.
    2.  Work with your internal network or security team to **allow outbound HTTPS (TCP port 443) access** to the specific domains listed in the failure message.
    3.  After your team has updated the firewall rules, return to **Templates** (Step 3) and **re-launch** the `Run Firewall Check` job to confirm the issue is resolved.

> **Important:** Please use these results as a diagnostic starting point. A failure is a strong indicator of a blocking rule, but it can occasionally be a **false positive** caused by a temporary network issue.

* **Next Steps:**
    1.  Review the job output log. The playbook is designed to show *which* domain(s) failed the connection test.
    2.  As a first step, work with your internal network or security team to **verify** that outbound HTTPS (TCP port 443) access is allowed for the domains listed in the failure message.
    3.  After your team has **confirmed or updated** the firewall rules, return to **Templates** (Step 3) and **re-launch** the `Run Firewall Check` job to confirm the fix.
    4.  If the job continues to fail, but your team has confirmed the rules are correct, please contact our support team for further investigation.
