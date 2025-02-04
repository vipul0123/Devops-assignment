![Screenshot 2024-02-17 120729](https://github.com/user-attachments/assets/1827f3aa-9418-4dd1-91fd-cd5e34849bae)
Explanation of different pipeline stages (Build, Test, Deploy) 



Here's a well-formatted and decorated version of your explanation with improved readability and clarity:  

---

# **CI/CD Pipeline Stages: Build, Test & Deploy**  

A Continuous Integration/Continuous Deployment (CI/CD) pipeline automates the software development lifecycle, ensuring efficient code integration, testing, security scanning, and deployment. Below are the different stages of a robust pipeline:  

---

## **🚀 Build Stages**  

### **1️⃣ Git Checkout - Fetching the Latest Code**  
🔹 **Purpose**: Pulls the latest source code from the GitHub repository, ensuring the pipeline always works with the most recent version.  

**Jenkins Pipeline Code:**  
```groovy
stage('Git Checkout') { 
    steps { 
        git branch: 'main', url: 'https://github.com/vipul0123/SMVDU-Yoga-Club.git' 
    } 
}
```

---

### **2️⃣ Compile - Building the Application**  
🔹 **Purpose**: Uses **Maven** to compile the Java source code into executable bytecode.  

**Jenkins Pipeline Code:**  
```groovy
stage('Compile') { 
    steps { 
        sh "mvn compile" 
    } 
}
```

---

## **🛡️ Test & Security Analysis Stages**  

### **3️⃣ SonarQube Analysis - Static Code Review**  
🔹 **Purpose**: Scans the code for **bugs, vulnerabilities, and code smells** using SonarQube.  

**Jenkins Pipeline Code:**  
```groovy
stage('SonarQube Analysis') { 
    steps { 
        withSonarQubeEnv('sonar') { 
            sh ''' 
                $SCANNER_HOME/bin/sonar-scanner 
                -Dsonar.projectName=SMVDU 
                -Dsonar.projectKey=SMVDU 
                -Dsonar.java.binaries=. 
            ''' 
        } 
    } 
}
```

---

### **4️⃣ OWASP Dependency Check - Security Scanning**  
🔹 **Purpose**: Scans **third-party libraries** for known security vulnerabilities.  

**Jenkins Pipeline Code:**  
```groovy
stage('OWASP Dependency Check') { 
    steps { 
        dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'DC'
        dependencyCheckPublisher pattern: '**/dependency-check-report.xml' 
    } 
}
```

---

### **5️⃣ Trivy Scan - Container Security**  
🔹 **Purpose**: Scans the **Docker image** for security vulnerabilities using Trivy.  

**Jenkins Pipeline Code:**  
```groovy
stage('Trivy Scan') { 
    steps { 
        sh "trivy image vipul0123/mywebsite > trivy-report.txt" 
    } 
}
```

---

## **📦 Deployment Stages**  

### **6️⃣ Docker Build - Creating a Container Image**  
🔹 **Purpose**: Builds a **Docker image** from the application source code.  

**Jenkins Pipeline Code:**  
```groovy
stage('Docker Build') { 
    steps { 
        sh "docker build -t vipul0123/mywebsite:latest ." 
    } 
}
```

---

### **7️⃣ Docker Push - Publishing the Image**  
🔹 **Purpose**: Pushes the built **Docker image** to **Docker Hub**.  

**Jenkins Pipeline Code:**  
```groovy
stage('Docker Push') { 
    steps { 
        sh "docker push vipul0123/mywebsite" 
    } 
}
```

---

### **8️⃣ Kubernetes Deployment - Running in Production**  
🔹 **Purpose**: Deploys the Docker container to a **Kubernetes cluster**. Uses `kubectl` to manage deployment and services.  

**Jenkins Pipeline Code:**  
```groovy
stage('Kubernetes Deploy') { 
    steps { 
        withKubeConfig(credentialsId: 'k8-token', namespace: 'webapps', serverUrl: 'https://172.31.41.52:6443') { 
            sh 'kubectl delete deployment ekart-deployment -n webapps' 
            sh "kubectl apply -f deploymentservice.yml -n webapps" 
            sh "kubectl get svc -n webapps" 
        } 
    } 
}
```

---

## **🔑 Managing Environment Variables & Secrets in Jenkins**  

🔹 **Why?** Environment variables store **credentials, API keys, and other configurations** securely.  

**Example - Setting Environment Variables in Jenkins:**  
```groovy
environment { 
    SCANNER_HOME = tool 'sonar-scanner' 
}
```
✅ **SCANNER_HOME** is assigned using Jenkins tools to ensure the correct **Sonar Scanner** binary is used.

---

## **📌 Summary of CI/CD Pipeline Stages**  

| **Stage**                      | **Purpose**                                      |
|--------------------------------|------------------------------------------------|
| **Git Checkout**               | Fetches the latest source code from GitHub.     |
| **Compile**                    | Compiles Java code using Maven.                 |
| **SonarQube Analysis**         | Performs static code analysis for security.    |
| **OWASP Dependency Check**     | Scans dependencies for vulnerabilities.       |
| **Trivy Scan**                 | Scans Docker images for security risks.       |
| **Docker Build**               | Builds the application into a Docker image.   |
| **Docker Push**                | Pushes the Docker image to Docker Hub.        |
| **Kubernetes Deployment**      | Deploys the application to a Kubernetes cluster. |

---

