# jenkins-ansible-docker-react

app: ![image](https://github.com/user-attachments/assets/bf8067d4-6c68-4c5d-bc74-ed902c470f7d)
jenkins img : ![image](https://github.com/user-attachments/assets/78c1e434-1ae5-4356-98e7-5b704284dbd6)

chatgpt link : https://chatgpt.com/c/681c8ea9-4e28-8011-8c3b-466ef35ae2f5

### **Overview of the Deployment Process**

This process automates the **deployment** of a **React application** using **Docker**, **Jenkins**, and **Ansible** on **AWS EC2 instances**.

### **1. React Application Development (VS Code)**

* **What:** You develop the React app locally using VS Code.
* **Why:** React is a popular framework for building dynamic, single-page web apps. VS Code is a lightweight IDE for writing and editing code.

### **2. Dockerize the React Application**

* **What:** Create a `Dockerfile` to containerize the React app.
* **Why:** Docker allows packaging your app with all its dependencies into a portable container, making it easy to deploy and run anywhere, whether locally or on a cloud server.

### **3. App Hosting EC2 (AWS Instance)**

* **What:** An EC2 instance is created to host the app.
* **Why:** The EC2 instance acts as the server that will run the containerized application.

### **4. Jenkins (CI/CD Automation)**

* **What:** Use Jenkins to automate the **build** and **deployment** process.

* **Why:** Jenkins automates workflows, helping to trigger the build and deploy pipeline whenever changes are made to the code, reducing manual intervention.

* **Steps:**

  1. **Jenkins setup**: Jenkins pulls the latest code from GitHub.
  2. **Run shell commands**: It then SSHs into the App Hosting EC2, stops and removes previous containers, rebuilds the app's Docker image, and deploys the new one.
  3. **Continuous Integration/Continuous Deployment (CI/CD)**: This ensures that any change in the code automatically triggers a new deployment to the EC2 instance, providing faster and error-free releases.

### **5. Ansible (Automation of Infrastructure Setup)**

* **What:** Ansible automates the installation and configuration of Docker on new instances.

* **Why:** Ansible is used to manage configurations across multiple servers. It simplifies the process of setting up and configuring infrastructure, such as installing Docker on EC2 instances.

* **Steps:**

  1. Create an **Ansible playbook** to install Docker and set it up on the App Hosting EC2.
  2. Ansible connects to the EC2 instance and installs Docker, ensuring that all necessary dependencies are installed and the app runs in Docker containers.

### **6. Deployment Flow**

* **What:** The deployment happens in the following stages:

  1. **React Development:** Write and test the React app on your local machine.
  2. **Dockerize:** Write a `Dockerfile`, build the Docker image for your app, and push it to the EC2 instance.
  3. **Automated CI/CD (Jenkins):** Whenever you push code changes to GitHub, Jenkins pulls the new code, builds a fresh Docker image, and deploys it to the EC2 instance.
  4. **Infrastructure Setup (Ansible):** Ansible ensures that Docker is installed and configured on the EC2 instance.

---

### **Purpose and Benefits of Each Tool**

1. **React**:

   * **Purpose:** Used for building user interfaces, particularly for single-page applications (SPAs).
   * **Benefit:** Offers a fast, scalable, and maintainable framework for building dynamic web apps.

2. **Docker**:

   * **Purpose:** Containers the application with all its dependencies to ensure it runs consistently across different environments.
   * **Benefit:** Reduces the "it works on my machine" problem, ensures portability, and simplifies deployment.

3. **Jenkins**:

   * **Purpose:** Automates the CI/CD pipeline, triggering automatic builds and deployments.
   * **Benefit:** Speeds up the development process, ensures code quality by testing and deploying automatically with every code change, and eliminates manual deployment steps.

4. **Ansible**:

   * **Purpose:** Automates infrastructure management and configuration tasks, like installing Docker or setting up servers.
   * **Benefit:** Reduces manual configuration, ensures consistency, and saves time by automating repetitive infrastructure tasks.

---

### **Summary of the Whole Architecture**

* **Local Development:** The React app is developed locally using VS Code.
* **Dockerization:** The app is containerized using Docker, ensuring consistency in deployment.
* **Deployment Automation:** Jenkins automates the entire process—pulling code from GitHub, building the Docker image, and deploying it to an AWS EC2 instance.
* **Infrastructure Automation:** Ansible automates the setup and configuration of Docker on the EC2 instance, ensuring that the environment is ready to run the app.
* **App Hosting:** The React app runs on an EC2 instance, with Docker ensuring that the application is isolated and portable.

By combining React, Docker, Jenkins, and Ansible, this process automates both the deployment of your app and the setup of infrastructure, providing a scalable, repeatable, and efficient deployment pipeline.

![ChatGPT Image May 8, 2025, 02_20_55 PM](https://github.com/user-attachments/assets/07d46570-dcb2-481b-9988-b76e10aa474f)



# execute shell

ssh -o StrictHostKeyChecking=no ubuntu@<APP_EC2_PUBLIC_IP> << 'EOF'
cd ~/react-devops-demo
git pull origin main
docker stop react-devops-demo || true
docker rm react-devops-demo || true
docker rmi react-devops-demo || true
docker build -t react-devops-demo .
docker run -d -p 80:80 --name react-devops-demo react-devops-demo
EOF


# docker-setup.yml -> ansible playbook

---
- hosts: web
  become: true
  tasks:
    - name: Install Docker
      apt:
        name: docker.io
        state: present

    - name: Start Docker
      service:
        name: docker
        state: started
        enabled: true

    - name: Add user to docker group
      user:
        name: ubuntu
        groups: docker
        append: yes



