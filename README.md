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
└── portfolio-project/
├── README.md
└── jenkins_docker.yml


- `README.md` – this documentation file
- `jenkins_docker.yml` – Ansible playbook to install Docker and configure user permissions

---

## Step-by-Step Process

## 1. Create Project Directory and Repository

On the local machine (or directly on the control server):

'''bash
mkdir -p ~/new_project/portfolio-project
cd ~/new_project/portfolio-project
git init

Created an empty repository on GitHub called portfolio-project

Cloned the repository locally:

git clone <github-repo-url> .


# Created and tracked the README.md file:

touch README.md
git add README.md
git commit -m "Add README"


## 2. Create Ansible Playbook

Created the Ansible playbook jenkins_docker.yml to:

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


Tracked and committed the playbook:

git add jenkins_docker.yml
git commit -m "Add Ansible playbook to install Docker and set permissions"
git push origin main



## 3. Launch EC2 Instances on AWS

    Launched two EC2 instances:

     - Control server – where Ansible is installed

     - Target server – where Docker and Jenkins are installed

    Installed Ansible on the control server:

     sudo apt update
     sudo apt install ansible -y


    Installed Jenkins on the target server:

     sudo apt update
     sudo apt install jenkins -y


    Set inbound security group rules for port 8080 on the target server to allow Jenkins access.
   


4. Set Up Passwordless SSH

On the control server:

Generate SSH key pair:

ssh-keygen -t ed25519


Copy the public key to the target server’s authorized_keys:

ssh-copy-id -i ~/.ssh/id_ed25519.pub ubuntu@TARGET_IP


Alternatively, manually append the public key to /home/ubuntu/.ssh/authorized_keys on the target server.

Now, the control server can SSH into the target server without a password:

ssh ubuntu@TARGET_IP



5. Clone Repository on Control Server

On the control server:

git clone <github-repo-url> portfolio-project
cd portfolio-project



6. Create Ansible Inventory File

Created inventory file to store the target server’s IP address:

[all]
target ansible_host=TARGET_IP ansible_user=ubuntu



7. Run Ansible Playbook

Execute the playbook to install Docker and configure users:

ansible-playbook -i inventory jenkins_docker.yml


Ansible connects to the target server via SSH and runs the tasks.

Output shows ok or changed for each task.



8. Verify Docker and User Permissions

Connect to the target server from the control server:

ssh ubuntu@TARGET_IP


Check Docker installation:

docker --version
docker ps


Check user group membership:

groups ubuntu
groups jenkins

