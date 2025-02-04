![Screenshot 2024-02-17 120729](https://github.com/user-attachments/assets/1827f3aa-9418-4dd1-91fd-cd5e34849bae)
Explanation of different pipeline stages (Build, Test, Deploy) 



1. Build Stages
    
a->Git Checkout

Pulls the source code from the GitHub repository.
Ensures the pipeline is always working with the latest version of the code.

stage('git checkout') {
    steps {
        git branch: 'main', url: 'https://github.com/vipul0123/SMVDU-Yoga-Club.git'
    }
}

b->Compile

Uses Maven to compile the Java source code.

stage('compile') {
    steps {
        sh "mvn compile"
    }
}

2. Test & Security Analysis Stages

c->SonarQube Analysis (Static Code Analysis)

Performs static code analysis using SonarQube.
Detects bugs, vulnerabilities, and code smells.


stage('sonarqube analysis') {
    steps {
       withSonarQubeEnv('sonar') {
           sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=SMVDU \
            -Dsonar.projectKey=SMVDU -Dsonar.java.binaries=. '''
        }
    }
}

d->OWASP Dependency Check

Scans project dependencies for security vulnerabilities.

stage('OWASP dependency check') {
    steps {
        dependencyCheck additionalArguments: '--scan ./ ', odcInstallation: 'DC'
        dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
    }
}

e->Trivy Scan (Container Security)

Scans the Docker image for vulnerabilities.

stage('trivy scan') {
    steps {
        sh "trivy image vipul0123/mywebsite>trivy-report.txt"
    }
}

3. Deployment Stages

f->Docker Build

Builds a Docker image from the source code.

stage('docker build') {
    steps {
       sh "docker build -t vipul0123/mywebsite:latest ."
    }
}

g->Docker Push

Pushes the built Docker image to Docker Hub.

stage('docker push') {
    steps {
        sh "docker push vipul0123/mywebsite"
    }
}

h->Kubernetes Deployment

Deploys the Docker container to a Kubernetes cluster.
Uses kubectl to apply the Kubernetes deployment and service files.

stage('kubernetes deploy') {
    steps {
        withKubeConfig(credentialsId: 'k8-token', namespace: 'webapps', serverUrl: 'https://172.31.41.52:6443') {
            sh 'kubectl delete deployment ekart-deployment -n webapps'
            sh "kubectl apply -f deploymentservice.yml -n webapps"
            sh "kubectl get svc -n webapps"
        }
    }
}

Summary of Pipeline Stages
Stage	Purpose
Git Checkout	Pulls source code from GitHub.
Compile	Compiles the Java project using Maven.
SonarQube Analysis	Performs static code analysis for bugs & vulnerabilities.
OWASP Dependency Check	Scans dependencies for security vulnerabilities.
Docker Build	Builds the application as a Docker image.
Trivy Scan	Scans Docker images for security vulnerabilities.
Docker Push	Pushes the Docker image to Docker Hub.
Kubernetes Deploy	Deploys the application to a Kubernetes cluster.

