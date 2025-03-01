
![image](https://github.com/user-attachments/assets/e426633f-0a81-41ad-b7e5-0353952433f8)

![image](https://github.com/user-attachments/assets/d70f8c26-32a4-4bc1-8279-4939c87501ec)

![image](https://github.com/user-attachments/assets/67d70d54-8769-4d5f-b18f-450e5e154908)

# Intro

Welcome to the system hardening lab. This lab will guide you through the process of securing a lightly provisioned RHEL 9 machine with a mid-level compliance score to level 1 and level 2 standards in accordance with the Center for Internet Security (CIS). You will use OpenSCAP (an open source security compliance solution that provides automated configuration, vulnerability, and patch checking) to generate system audits before remediation, a script to remediate the failures using an Ansible playbook, followed by a post-remediation system audit to assess your progress. 

# Prerequisites

Equipment Needed:

- **A RHEL 9 Host Machine or VM with approximately 2 CPU cores, 4GB of RAM, and 20GB of Storage Space**
    - Installation Manager Configurations listed below:
  
    ![image](https://github.com/user-attachments/assets/69729a0c-d51c-4fab-add8-fc7845d201c1)

    ![image.png](https://github.com/user-attachments/assets/343ed528-c23a-485b-98ed-3089b546575c)
    

Before applying CIS hardening, ensure the following requirements are met:

### **1️⃣ Install Ansible (If Not Already Installed)**

If Ansible is not installed, install it using:

```
sudo dnf install -y ansible-core
```

Verify the installation:

```
ansible --version
```

### **2️⃣ Install Required Ansible Collections (If Needed)**

Ensure `ansible.posix` and `community.general` collections are installed:

```
ansible-galaxy collection install ansible.posix community.general
```

*NOTE: If you encounter “ini_file” or “systctl” related errors when running the Ansible playbook, these collections should resolve it.*

### **3️⃣ Ensure Python 3 is Installed and Used**

```
python3 --version
ansible --version
```

If necessary, set Python 3 as the default:

```
sudo alternatives --config python
```

---

### **Configuration Changes & Required Packages**

The following packages must be installed for CIS hardening:

```
sudo dnf install -y audit audit-libs auditd aide policycoreutils-python-utils
```

*NOTE: auditd might flag an error, if so just ensure it is enabled in the next step*

Additionally, ensure services like `auditd` are enabled:

```
sudo systemctl enable --now auditd
```

### **Modify `/etc/audit/audit.rules` for Compliance**

Check if the audit rules exist:

```
cat /etc/audit/audit.rules
```

If necessary, add CIS-compliant rules:*

```
echo '-w /var/log/sudo.log -p wa -k actions' | sudo tee -a /etc/audit/rules.d/audit.rules
sudo auditctl -R /etc/audit/rules.d/audit.rules
```

### **Modify `/etc/security/limits.conf` for User Restrictions**

Ensure CIS-recommended settings are present:*

```
echo '* hard core 0' | sudo tee -a /etc/security/limits.conf
```

### **Ensure Sysctl Kernel Parameters Are Configured**

```
echo 'net.ipv6.conf.all.accept_ra = 0' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

![image 2](https://github.com/user-attachments/assets/3536bec1-c916-4875-894b-44c988049fbd) ![image 3](https://github.com/user-attachments/assets/04690081-df1e-4de1-9ada-eb64221c6ec8)




### *NOTE: Create a snapshot of your machine at this point so you have a baseline to revert back to in case of failures.*

---

# Level 1

# **CIS Hardening RHEL 9 with OpenSCAP & Ansible**

---

## **📝 Step 1: Install OpenSCAP & Security Tools**

### **1. Update the system***

```bash
sudo dnf update -y

```

### **2. Install OpenSCAP & Security Tools***

```bash
sudo dnf install -y scap-security-guide openscap-utils openscap-scanner 

```

### **3. Verify OpenSCAP Installation***

```bash
oscap --version

```

---

## **🔍 Step 2: Perform a Security Audit with OpenSCAP**

### **1. Identify Available Security Profiles***

```bash
oscap info /usr/share/xml/scap/ssg/content/ssg-rhel9-ds.xml | grep -i "cis"

```

Look for:

✅ `xccdf_org.ssgproject.content_profile_cis_server_l1` (CIS Level 1 Compliance for Servers)

### **2. Run a Security Audit**

Navigate to `/usr/share/xml/scap/ssg/content`

**Level 1 Report ****

```bash
sudo oscap xccdf eval --profile xccdf_org.ssgproject.content_profile_cis_server_l1 --results rhel9-cis-l1-report.xml
--report rhel9-cis-l1-report.html 
/usr/share/xml/scap/ssg/content/ssg-rhel9-ds.xml

```

We will be using the server profiles for this lab:

🔹 **Outputs:**

- **Audit report:** `rhe9-cis-l1-report.html`
- **Results file:** `rhel9-cis-l1-results.xml`

### **3. Review the Report**

- Export and Open `rhel9-cis-l1-report.html` in a browser.
    - Use `scp` on the destination host (Example:`scp user[@](mailto:jalin@192.168.10.124)source:/usr/share/scap-security-guide/ansible/rhel9-cis-l1-report.html C:\Users\<User>\OneDrive\Desktop`)
- Note any **critical** or **high-risk** issues.

***Expected Location of the Report:***

`/usr/share/xml/scap/ssg/content`

![image 4](https://github.com/user-attachments/assets/68a68a0c-9d7a-43d8-929e-c30c65acd559)

---

## **🛠 Step 3: Generate an Ansible Playbook for Hardening**

### **1. Create a Playbook for Fixing Compliance Issues**

```bash
sudo oscap xccdf generate fix --profile xccdf_org.ssgproject.content_profile_cis_server_l1 --fix-type ansible rhel9-cis-report-l1.xml > rhel9-cis-l1-fix.yml

```

🔹 This creates `rhel9-cis-l1-fix.yml`, a **CIS-compliant Ansible playbook**.

### **2. Review the Playbook**

```bash
nano rhel9-cis-l1-fix.yml

```

---

## **🚀 Step 4: Apply CIS Hardening**

### 1. Define host in an inventory.ini file

```bash
vi or nano inventory.ini
[all:vars]

[local]
your_host_ip  ansible_connection=local

```

### 2. Run the playbook targeting [`localhost`](http://localhost) in Dry-Run Mode: **(Check for Issues)**

```bash
ansible-playbook -i inventory.ini rhel9-cis-l1-fix.yml --check

```

🔹 This **simulates** changes without applying them.

### **3. Apply the Playbook**

```bash
ansible-playbook -i inventory.ini rhel9-cis-l1-fix.yml 
```

Additional Options (if applicable)

### **Run the CIS Level 1 Hardening Playbook to Specified Target Hosts**

**If you want to target a specific remote server:**

```bash
ansible-playbook -i inventory.ini rhel9-cis-l1-fix.yml --limit "webserver1,webserver2"

```

*NOTE: The playbook can take up to an hour to complete on this test machine
CRITICAL:* 

- *After the completion of the playbook, you will not be able to access the root user via SSH any further,*
- *You might also get locked out of  root and non-root users due to tightened password policies. Use this [**GUIDE**](https://www.notion.so/CIS-Benchmark-Procedure-RHEL-9-1a7391617bd2802ab104d82606371ab8?pvs=21) if necessary to reset expired passwords*
- If using port essential services (i.e Splunk Web) exist on the machine, you will temporarily lose access due to the installation of firewalld and restriction of loopback traffic.
    - Use the following commands to restore necessary ports
    
    ```bash
    sudo firewall-cmd --add-port=<port>/tcp --permanent
    sudo firewall-cmd --reload
    ```
    

---

## **🔄 Step 5: Re-Audit for Compliance**

After applying hardening, **run OpenSCAP again** to check compliance:

```bash
sudo oscap xccdf eval --profile xccdf_org.ssgproject.content_profile_cis_server_l1 \
--report rhel9-cis-l1-report-post.html --results rhel9-cis-results-post.xml \
/usr/share/xml/scap/ssg/content/ssg-rhel9-xccdf.xml

```

Compare **`rhel9-cis-report-post.html`** with the previous report.

![image 5](https://github.com/user-attachments/assets/9a167946-3e30-4d0b-bd95-51fdac968dc8)

### *NOTE: Some tasks in the script do not have auto remediation functionality. Look into your report to find additioanl suggestions.*
### *NOTE: Create another snapshot of your Level 1 hardened machine at this point.*

---

# Challenge

# Level 2

- Using what you've learned from the Level 1 run, generate a Level 2 audit, remediation script, and post-script report. 😎

# TIPS

- **Running tasks where you left off previously in case of interruption**
    - `ansible-playbook <playbook>.yml --start-at-task="TASK_NAME”`

---

## **🎯 Summary**

✅ **Performed OpenSCAP Audit**

✅ **Generated an Ansible Playbook for CIS Compliance**

✅ **Re-Audited to Ensure Compliance**

---

# User Lockout Guide (if needed)

### 

1. Reboot the system. When the GRUB bootloader screen appears, use the **Up Arrow** and **Down Arrow** keys to stop the countdown timer. Be quick, as the timer may be only 1-5 seconds.
2. Press `e`to edit the commands before booting
3. On the **linux** line (ending with “quiet”): Remove all **console=** and **vconsole=** key=value pairs (if applicable). Add **init=/bin/bash** to the end of the line. Press **Ctrl+X** to continue.
    
 ![image 6](https://github.com/user-attachments/assets/1a2610fd-e1c4-44ce-a084-232ef06a9f59)
   
4. At the **bash-5.1#** prompt, remount the system's root as read/write:

```bash
mount -o remount,rw /
```

1. Change the root and necessary non-root passwords:

```bash
passwd root
passwd <user>
```

1. Create the SELinux autorelabel file:

```bash
touch /.autorelabel
```

*Warning: Do not make typos in this command or you'll need to start over.*

1. Reboot the system:

```bash
reboot -f
```

This should get you back into your machine. Return to [Step 5](https://www.notion.so/CIS-Benchmark-Procedure-RHEL-9-1a7391617bd2802ab104d82606371ab8?pvs=21)

---
# Conclusion 

Congratulations on completing the System Hardening Lab! By following this guide, you've successfully transformed a lightly provisioned RHEL 9 machine into a security-hardened environment that meets CIS compliance standards.
Throughout this process, you've gained valuable hands-on experience with:

System Auditing - Using OpenSCAP to identify vulnerabilities and compliance gaps in your system

Automated Remediation - Implementing security controls through Ansible playbooks

Verification Processes - Confirming the effectiveness of security measures through post-remediation audits

Industry Standards - Applying Center for Internet Security (CIS) benchmarks to real-world systems

Here are some additional challenges you can attempt from here:

- Try Level 3 (STIG) hardening  
- Try hardening different types of systems (Ubuntu, Windows, etc.) using similar methodologies
- Install additional services/daemons to simulate production servers running critical business functions and find out what breaks. 
