# Portfolio Project: Jenkins and Docker Setup with Ansible on AWS EC2

## Project Overview

This project demonstrates how to automate the installation and configuration of Docker on an EC2 instance using **Ansible**, with a Jenkins master server acting as an agent. It showcases:

- Creation of a structured project repository
- Version control with Git and GitHub
- Infrastructure setup on AWS EC2
- Passwordless SSH authentication between control and target servers
- Running an Ansible playbook to install Docker and set user permissions

---

## Project Structure

The project directory structure is as follows:

new_project/
portfolio-project/
README.md
jenkins_docker.yml
inventory

- `README.md` – this documentation file
- `jenkins_docker.yml` – Ansible playbook to install Docker and configure user permissions
- `inventory` – stores the target server IP address for Ansible

---

## Step-by-Step Process

### 1. Create Project Directory and Repository

On the local machine (or directly on the control server):


    mkdir -p ~/new_project/portfolio-project
    cd ~/new_project/portfolio-project
    git init
    
Create an empty repository on GitHub called portfolio-project

Clone the repository locally:

    git clone <github-repo-url> .
    Created and tracked the README.md file:

    touch README.md
    git add README.md
    git commit -m "Add README"


### 2. Create Ansible Playbook
Create the Ansible playbook jenkins_docker.yml to:

- Install Docker

- Start the Docker daemon

- Add both ubuntu and jenkins users to the Docker group

Example commands within the playbook (tasks):


    - name: Install Docker
      apt:
        name: docker.io
        state: present

    - name: Start Docker Daemon
      service:
        name: docker
        state: started

    - name: Add Ubuntu User to Docker Group
      user:
        name: ubuntu
        groups: docker
        append: yes

    - name: Add Jenkins User to Docker Group
      user:
        name: jenkins
        groups: docker
        append: yes
        
Track and committ the playbook:

    git add jenkins_docker.yml
    git commit -m "Add Ansible playbook to install Docker and set permissions"
    git push origin main

<img width="1098" height="938" alt="Screenshot (464)" src="https://github.com/user-attachments/assets/2b51e3a2-eec9-4353-bb38-ab7323abecb2" />



### 3. Launch EC2 Instances on AWS
Launch two EC2 instances:

- Control server – where Ansible is installed

- Target server – where Docker and Jenkins are installed

Install Ansible on the control server:

    sudo apt update
    sudo apt install ansible -y
    
Install Jenkins on the target server:

    sudo apt update
    sudo apt install jenkins -y #See Jenkins Documentation

<img width="1811" height="960" alt="Screenshot (452)" src="https://github.com/user-attachments/assets/3caae11d-8532-44d6-8813-86d8160696c4" />

Set inbound security group rules for port 8080 on the target server to allow Jenkins access.

<img width="1920" height="858" alt="Screenshot (465)" src="https://github.com/user-attachments/assets/75fe4846-2585-4a7d-b109-6f05cc9cb607" />

Copy TARGET_IP with port 8080 and paste on browser as TARGET_IP:8080 to view Jenkins page

<img width="1358" height="868" alt="Screenshot (460)" src="https://github.com/user-attachments/assets/8f48050b-3ef3-4443-b34c-dc7656f81664" />

<img width="1351" height="852" alt="Screenshot (462)" src="https://github.com/user-attachments/assets/6b343817-8286-4b21-a26a-c0a403b931b7" />



### 4. Set Up Passwordless SSH
On the control server:

Generate SSH key pair:

    ssh-keygen -t ed25519

<img width="1138" height="972" alt="Screenshot (448)" src="https://github.com/user-attachments/assets/f9013987-f95c-4672-90d7-48d0f5287426" />
    
Copy the public key to the target server’s authorized_keys:

    ssh-copy-id -i ~/.ssh/id_ed25519.pub ubuntu@TARGET_IP
    
    Alternatively, manually append the public key to /home/ubuntu/.ssh/authorized_keys on the target server.

Now, the control server can SSH into the target server without a password:

    ssh ubuntu@TARGET_IP

<img width="1083" height="981" alt="Screenshot (449)" src="https://github.com/user-attachments/assets/d01a4a98-c02a-4f2b-80a0-3c197ca1d548" />


    
### 5. Clone Repository on Control Server
On the control server:

    git clone <github-repo-url> portfolio-project
    cd portfolio-project

    
### 6. Create Ansible Inventory File
Created inventory file to store the target server’s IP address:

    vim inventory
    Paste TARGET_IP 

<img width="738" height="984" alt="Screenshot (451)" src="https://github.com/user-attachments/assets/ebd02bfa-7a67-4090-a670-c5b24951f61f" />


### 7. Run Ansible Playbook
Execute the playbook to install Docker and configure users:

    ansible-playbook -i inventory jenkins_docker.yml

<img width="1920" height="727" alt="Screenshot (455)" src="https://github.com/user-attachments/assets/ecbafa4b-0875-4e08-9101-6fcac5c47831" />

Ansible connects to the target server via SSH and runs the tasks.

Output shows ok or changed for each task.



### 8. Verify Docker and User Permissions
Connect to the target server from the control server:

    ssh ubuntu@TARGET_IP
    
Check Docker installation:

    docker --version
    docker ps
    
Check user group membership:

    groups ubuntu
    groups jenkins

<img width="1920" height="717" alt="Screenshot (458)" src="https://github.com/user-attachments/assets/7ffeb210-213f-4421-a732-c5dcf69bb6ac" />

Both users should include the docker group.


Summary
Successfully automated Docker installation and user permission setup on an EC2 instance using Ansible.

Passwordless SSH between control and target servers allows seamless playbook execution.

Repository is version-controlled on GitHub for future tracking and collaboration.


Commands Reference

# Project setup
mkdir -p ~/new_project/portfolio-project
cd ~/new_project/portfolio-project
git clone <repo-url> .

# Add README
touch README.md
git add README.md
git commit -m "Add README"
git push origin main

# SSH keygen on control server
ssh-keygen -t ed25519
ssh-copy-id -i ~/.ssh/id_ed25519.pub ubuntu@TARGET_IP

# Run Ansible playbook
ansible-playbook -i inventory 
jenkins_docker.yml

# Verify Docker and groups
docker --version
docker ps
groups ubuntu
groups jenkins





