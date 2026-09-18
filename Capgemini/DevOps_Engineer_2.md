# Capgemini

**Exp-9yrs Relevant(5yrs in devops)**

**Ansible**

1. What is an Ansible playbook?
--------------------------------------------------------------------------------------
An Ansible Playbook is a YAML file that contains a list of tasks Ansible should perform on one or more servers.
---
- name: Deploy Application
  hosts: webservers
  become: yes

  vars:
    app_name: nginx

  tasks:
  - name: Install Package
    apt:
      name: "{{ app_name }}"
      state: present

  - name: Start Service
    service:
      name: "{{ app_name }}"
      state: started

  **Run: ansible-playbook deploy.yml**
**This will:**
Connect to all webservers.
Install Nginx.
Start the Nginx service.
Use the variable app_name wherever needed.
**Easy way to remember**
Inventory = List of servers.
Playbook = List of tasks to perform.
Task = Single action (install package, copy file, restart service).
Variable = Reusable value ({{ app_name }}).
ansible-playbook = Command that executes the playbook.

--------------------------------------------------------------------------------------
2. How to install Ansible on Ubuntu and RedHat, and start services?
--------------------------------------------------------------------------------------
Install Ansible on Ubuntu
**Step 1:** Update packages
sudo apt update
**Step 2: Install Ansible**
sudo apt install ansible -y
**Step 3:** Verify installation
ansible --version
Example output:ansible [core 2.x.x]

Install Ansible on Red Hat / RHEL
sudo dnf install epel-release -y
sudo dnf install ansible -y

**Start a Service Using Ansible**
Suppose you want to start Nginx.
Playbook
---
- name: Start Nginx Service
  hosts: webservers
  become: yes

  tasks:
    - name: Start nginx
      service:
        name: nginx
        state: started
ansible-playbook start-nginx.yml

state: stopped
state: restarted

---------------------------------------------------------------------------------------
3. How to create 3 users and map them to prod, task, and QA groups in a single task?
4. How to include and input parameters in a playbook?
5. What are Ansible roles?
6. Use of templates in Ansible
7. Difference between templates and roles
8. How to initialize a role and push it to Ansible Galaxy for public use
9. How to encrypt a playbook?
10. How to execute a vault file?
11. How to use an environmental variable for passwords?
12. Create a MySQL dump from encrypted data using an Ansible playbook — how to execute the steps?
13. How to execute on all DB servers *excluding 1 server*?
14. How to shut down all servers using an ad-hoc command?
15. How to reduce execution time on RDBMS server?
16. How to do that from an Ansible file?
17. How to increase debug log level?
18. Difference between static and dynamic inventory

**Kubernetes**

1. What is the architecture of Kubernetes?
2. kubectl apply command – how the services are used?
3. Deployment YAML file
4. What are labels and annotations?
5. Traffic coming from outside to a cluster
6. What is the control manager’s task?
7. Node affinity and anti-affinity
8. How to run 2 pods with one depending on another – how to set roles?
9. What is taint and toleration?
10. Reason for taint in worker nodes
11. How to limit resources in Kubernetes?
12. Blue-Green deployment
13. Canary deployment
14. What is CSI?

**AWS**

1. AWS Lambda function and Step Function
2. What are autoscaling policies and their uses?
3. What is a target probe in autoscaling?
4. Which role to give to access services in AWS?
5. How to configure a role to service accounts?
6. Service account end-to-en
7. How to create a secret service account?
8. Static vs dynamic storage provisioning
9. Type of error in pod label
