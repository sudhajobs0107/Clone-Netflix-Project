# **Netflix Clone Project :-)**
### In this project we will create our own Netflix application using Jenkins, monitoring using Grafana and Prometheus, security using SonarQube,Trivy. 
![](https://github.com/sudhajobs0107/Netflix/blob/main/public/assets/architecture.png)
___
## Tool we will need in this project is :-
>+ ### 1.) AWS EC2
>+ ### 2.) Docker and Dockerhub
>+ ### 3.) Github  
>+ ### 4.) Jenkins
>+ ### 5.) SonarQube
>+ ### 6.) Trivy
>+ ### 7.) Grafana
>+ ### 8.) Prometheus
___
# Prerequisites
### Before starting the project you should have these things in your system :-
>+ ### Account on AWS
>+ ### Git
>+ ### Code (we will use code from this repository) : [link with code](https://github.com/sudhajobs0107/Netflix)
___
# **Part 1** : **Initial Setup and Deployment**
## STEP 1: Launch Instance
+ ### Create AWS EC2 instance (t2.large)
![](https://github.com/sudhajobs0107/Netflix/blob/main/public/assets/netflix-jenkins.PNG)

+ ### We can also attach a Elastic IP address because it,s a big project if you want to do this project in parts then it will help.
+ ### Connect to instance
___
## STEP 2: Clone the Git Code Repo to  EC2 instance
+ ### Use command as follow :-
```
git clone url
```
+ ### This will fetch the code from the repo. Now go into directory :-
```
cd DevSecOps-Project/
ls
```
+ ### And we will see files of the project
![](https://github.com/sudhajobs0107/Netflix/blob/main/public/assets/files.PNG)


## STEP 3: Install Docker and Run the App Using a Container
+ ### Use command as follow :-
```
sudo apt-get update
sudo apt-get install docker.io -y
sudo usermod -aG docker $USER
newgrp docker
sudo chmod 777 /var/run/docker.sock
```
+ ### Check docker install or not,use command  :-
```
docker version
```
+ ### Next command to build docker container :-
```
docker build -t netflix .
```
![](https://github.com/sudhajobs0107/Netflix/blob/main/public/assets/16%20steps.PNG)

## STEP 4: API Key
+ ### Go to TMDB (The Movie Database) website.
+ ### Click on "Login" => create an account => go to your profile => select "Settings" => click on "API" => create a new API key by clicking "Create" => fill details => Submit => We will receive our TMDB API key.
+ ### Use command as follow :-
```
sudo systemctl start jenkins.service
```
+ ### Next command to run docker container :-
```
docker run -d --name netflix -p 8081:8080 (image id)
```
+ ### Now copy **Public IPv4 address:8081** and you will see :- 
![](https://github.com/sudhajobs0107/Netflix/blob/main/public/assets/application%20blank.PNG)

+ ### Here we will see a black netflix application because we haven't provide any API key. **API is used to fetch the data from somewhere into our application**.
+ ### Now if any container is running then stop and remove that container, for this use command :-
```
docker ps
docker stop <containerid>
docker rm <containerid>
```
+ ### Now recreate the Docker image with your api key, use command :-
```
docker build --build-arg TMDB_V3_API_KEY=your-api-key -t netflix .
```
+ ### To check docker image build or not use command :-
```
docker images
```
+ ### And we will see our docker image is build :-
![](https://github.com/sudhajobs0107/Netflix/blob/main/public/assets/docker-image.PNG)
+ ### Next command :-
```
docker run -d --name netflix -p 8081:8080 (image id)
```
+ ### Now our application will having all the movies and everything :-
![](https://github.com/sudhajobs0107/Netflix/blob/main/public/assets/netflix-movies.PNG)

___


# **Part 2** : **Install SonarQube and Trivy**
## STEP 1: Fetch SonarQube image from Dockerhub to our machine, use command :-
```
docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
```
![](https://github.com/sudhajobs0107/Netflix/blob/main/public/assets/sonar.PNG)

+ ### Now copy **Public IPv4 address:9000** and you will see :- 
![](https://github.com/sudhajobs0107/Netflix/blob/main/public/assets/sonarqube-image.PNG)

+ ### To install Trivy, use command :-
```
sudo apt-get install wget apt-transport-https gnupg lsb-release
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy
```
+ ### To check Trivy install or not, use command :-
```
trivy
```
+ ### To check Trivy version, use command :-
```
trivy version
```
# Trivy is a open source security tool. It is used to scan file systems and give us a report, what is the problem with our file systems.
+ ### To scan the filesystem, use command :-
```
trivy fs .
```
### ( **.** refers current one)
![](https://github.com/sudhajobs0107/Netflix/blob/main/public/assets/trivy.PNG)



___
# **Part 3** : **CI/CD Setup**
+ ### While installing Jenkins, we also need to have Java installed so for Java use command :-
```
sudo apt update
sudo apt install fontconfig openjdk-17-jre
java -version
openjdk version "17.0.8" 2023-07-18
OpenJDK Runtime Environment (build 17.0.8+7-Debian-1deb12u1)
OpenJDK 64-Bit Server VM (build 17.0.8+7-Debian-1deb12u1, mixed mode, sharing)
```
+ ### For Jenkins use command :-
```
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
/etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
sudo systemctl start jenkins
sudo systemctl enable jenkins
```
+ ### To check Jenkins status, use command :-
```
sudo service jenkins status
```
+ ### Now copy **Public IPv4 address:8080** and we will be on **Unlock Jenkins page**.
+ ### To unlock jenkins, use command :-
```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
+ ### And we will get our password. Copy and paste it to unlock Jenkins => Now click Install suggested plugins => Fill details => Welcome to Jenkins
![](https://github.com/sudhajobs0107/Netflix/blob/main/public/assets/welcome-to-jenkins.PNG)

+ ### Now we have to Install Necessary Plugins for this, Go to Manage Jenkins → Plugins → Available Plugins :-
>+ ### 1.) Eclipse Temurin Installer
>+ ### 2.) SonarQube Scanner
>+ ### 3.) NodeJs Plugin 
### Now we have to set Plugins for this, Go to Manage Jenkins → Tools → Install JDK(17) and NodeJs(16)→ Click on Apply and Save
### Now create token in SonarQube for this, Go to SonarQube → Administrator → Security → Update Tokens → name "jenkins" → Generate
### Copy this token and come back to Jenkins → Go to Manage Jenkins → click on Credentials → System → Global credentials → Add Credentials → Secret text → in Secret put the token that we copied in SonarQube → ID "Sonar-token" → Description "Sonar-token" → Create.
### Now again Go to Manage Jenkins → System → Install SonarQube → Server URL → in Server authentication token select "Sonar-token" → click on Apply and Save
### Now configure sonar scanner for this Go to Manage Jenkins → Tools  → click "Add SonarQube Scanner" → name "sonar-scanner" → click on Apply and Save
### Now click on **Create a job** => give name "**netflix**" => select "**Pipeline**" => click **OK** → Go to Pipeline section → in Script paste the code as given below :-
```
pipeline {
    agent any
    tools {
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }
    stages {
        stage('clean workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout from Git') {
            steps {
                git branch: 'main', url: 'https://github.com/sudhajobs0107/Netflix.git'
            }
        }
        stage("Sonarqube Analysis") {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Netflix \
                    -Dsonar.projectKey=Netflix'''
                }
            }
        }
        stage("quality gate") {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token'
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
    }
}
```
### Now click Apply and Save → **Build Now** and our pipeline will run.

### Now we have to Install somemore Plugins for this, Go to Manage Jenkins → Plugins → Available Plugins :-
>+ ### 1.) OWASP Dependency-Check (use this tool to integrate more security in our pipeline)
>+ ### 2.) Docker
>+ ### 3.) Docker Commons
>+ ### 4.) Docker Pipeline
>+ ### 5.) Docker API
>+ ### 6.) docker-build-step
## Now for login to dockerhub we need credential so for this, Go to Manage Jenkins → click on Credentials → System → Global credentials → Add Credentials → Username with password → put Username and Password → ID "docker" → Description "docker" → Create.
## Now configure Dependency-Check Tool and Docker Installations, Go to Manage Jenkins → Tools → Add Dependency-Check → name "DP-Check → tick "Install automatically" → Add Installer "Install from github.com" → click "Add docker" → name "docker" → tick "Install automatically" → docker version "latest" → click on Apply and Save
###  Now we can proceed with configuring our Jenkins pipeline to include these tools and credentials in our CI/CD process. First delete the container that we have already by using command :-
```
docker stop CONTAINER ID
docker rm CONTAINER ID
```
### Now add few new steps in our pipeline → Go to **Pipeline section** => click **Configure** → click **Pipeline** → in Script paste the code as given below :-
```

pipeline{
    agent any
    tools{
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }
    stages {
        stage('clean workspace'){
            steps{
                cleanWs()
            }
        }
        stage('Checkout from Git'){
            steps{
                git branch: 'main', url: 'https://github.com/sudhajobs0107/Netflix.git'
            }
        }
        stage("Sonarqube Analysis "){
            steps{
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Netflix \
                    -Dsonar.projectKey=Netflix '''
                }
            }
        }
        stage("quality gate"){
           steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token' 
                }
            } 
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
        stage('OWASP FS SCAN') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage('TRIVY FS SCAN') {
            steps {
                sh "trivy fs . > trivyfs.txt"
            }
        }
        stage("Docker Build & Push"){
            steps{
                script{
                   withDockerRegistry(credentialsId: 'docker', toolName: 'docker'){   
                       sh "docker build --build-arg TMDB_V3_API_KEY=<yourapikey> -t netflix ."
                       sh "docker tag netflix sudhajobs0107/netflix:latest "
                       sh "docker push sudhajobs0107/netflix:latest "
                    }
                }
            }
        }
        stage("TRIVY"){
            steps{
                sh "trivy image sudhajobs0107/netflix:latest > trivyimage.txt" 
            }
        }
        stage('Deploy to container'){
            steps{
                sh 'docker run -d -p 8081:80 sudhajobs0107/netflix:latest'
            }
        }
    }
}
```
# In pipeline we will get error related docker login, so to solve this problem use command :-
```
sudo su
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins
```

# Build Now and our pipeline build successfully.
![](https://github.com/sudhajobs0107/Netflix/blob/main/public/assets/success-pipeline.PNG)

___
# **Part 4** : **Monitoring**
### For monitoring we need t2.medium instance → Launch Instance  → Connect to instance, now to install and configure **Prometheus** use command as given below :-
```
sudo apt update -y
sudo useradd --system --no-create-home --shell /bin/false prometheus
wget https://github.com/prometheus/prometheus/releases/download/v2.47.1/prometheus-2.47.1.linux-amd64.tar.gz
```
### Now to see Prometheus is install or not use command :-
```
ls
```
### And we will see Prometheus is in **.zip folder** :-
![](https://github.com/sudhajobs0107/Netflix/blob/main/public/assets/zip.PNG)

### Now we need to **unzip** it, for this use command :-
```
tar -xvf prometheus-2.47.1.linux-amd64.tar.gz
cd prometheus-2.47.1.linux-amd64/
sudo mkdir -p /data /etc/prometheus
sudo mv prometheus promtool /usr/local/bin/
sudo mv consoles/ console_libraries/ /etc/prometheus/
sudo mv prometheus.yml /etc/prometheus/prometheus.yml
```
### To see Prometheus Promtool use command :-
```
cd /user/local/bin/
ls
```
### And we will see :-
![](https://github.com/sudhajobs0107/Netflix/blob/main/public/assets/prometheus-promtool.PNG)

### Now see ownership for directories, to see use command:-
```
ls -la /etc/prometheus/
```
![](https://github.com/sudhajobs0107/Netflix/blob/main/public/assets/owner.PNG)


### Now create a systemd unit configuration file for Prometheus, for this use command :-
```
sudo nano /etc/systemd/system/prometheus.service
```
### This open a another tab, in this tab add the following content to the prometheus.service file as given below :-
```
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

StartLimitIntervalSec=500
StartLimitBurst=5

[Service]
User=prometheus
Group=prometheus
Type=simple
Restart=on-failure
RestartSec=5s
ExecStart=/usr/local/bin/prometheus \
  --config.file=/etc/prometheus/prometheus.yml \
  --storage.tsdb.path=/data \
  --web.console.templates=/etc/prometheus/consoles \
  --web.console.libraries=/etc/prometheus/console_libraries \
  --web.listen-address=0.0.0.0:9090 \
  --web.enable-lifecycle

[Install]
WantedBy=multi-user.target
```
### Now enable and start Prometheus, for this use command :-
```
sudo systemctl enable prometheus
sudo systemctl start prometheus
```
### To check the status of Prometheus, use command :-
```
sudo systemctl status prometheus
```
![](https://github.com/sudhajobs0107/Netflix/blob/main/public/assets/prometheus-active.PNG)

+ ### Now copy **Public IPv4 address:9090** and you will see :- 
![](https://github.com/sudhajobs0107/Netflix/blob/main/public/assets/promethus-page.PNG)

### Now we have to install Node Exporter, use command :-
```
sudo useradd --system --no-create-home --shell /bin/false node_exporter
wget https://github.com/prometheus/node_exporter/releases/download/v1.6.1/node_exporter-1.6.1.linux-amd64.tar.gz
```
### Now extract Node Exporter files, move the binary, and clean up :-
```
tar -xvf node_exporter-1.6.1.linux-amd64.tar.gz
sudo mv node_exporter-1.6.1.linux-amd64/node_exporter /usr/local/bin/
rm -rf node_exporter*
```
### Now if we see our **node_exporter** is added, for this use command :-
```
ls /usr/local/bin/
```

### Now create a systemd unit configuration file for Node Exporter, for this use command :-
```
sudo nano /etc/systemd/system/node_exporter.service
```
### This open a another tab, in this tab add the following content to the node_exporter.service file as given below :-
```
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

StartLimitIntervalSec=500
StartLimitBurst=5

[Service]
User=node_exporter
Group=node_exporter
Type=simple
Restart=on-failure
RestartSec=5s
ExecStart=/usr/local/bin/node_exporter --collector.logind

[Install]
WantedBy=multi-user.target
```
### Now enable and start Node Exporter, for this use command :-
```
sudo systemctl enable node_exporter
sudo systemctl start node_exporter
```
### To check the status of Node Exporter, use command :-
```
sudo systemctl status node_exporter
```

## Go to **dockerhub** and see our **netflix image** has been created. So we have successfully created a pipeline which is going to get the code, create an image, do all the scanning, push image on dockerhub which can be used by anyone.
![](https://github.com/sudhajobs0107/Netflix/blob/main/public/assets/dockerhub.PNG)

### Now to configure Prometheus to scrape metrics from Node Exporter and Jenkins, we need to modify the prometheus.yml file so :-
```
sudo nano prometheus.yml
```
### And in the prometheus.yml file add as given below :-
```
scrape_configs:
  - job_name: 'node_exporter'
    static_configs:
      - targets: ['localhost:9100']
```
### To check the syntex correct or not, use command :-
```
promtool check config /etc/prometheus/prometheus.yml
```

### Now reload the Prometheus configuration without restarting, for this use command :-
```
curl -X POST http://localhost:9090/-/reload
```
![](https://github.com/sudhajobs0107/Netflix/blob/main/public/assets/target.PNG)
![](https://github.com/sudhajobs0107/Netflix/blob/main/public/assets/metrics.PNG)

___
# **Part 5** : Install **Grafana** and set it up to **Work with Prometheus**
## STEP 1: Install Dependencies, use command :-
```
sudo apt-get update
sudo apt-get install -y apt-transport-https software-properties-common
```
## Step 2: Add the GPG Key for Grafana, use command :-
```
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
```
## Step 3: Add the repository for Grafana stable releases, use command :-
```
echo "deb https://packages.grafana.com/oss/deb stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
```
## Step 4: Update the package list and install Grafana, use command :-
```
sudo apt-get update
sudo apt-get -y install grafana
```
## Step 5: To enable and Start Grafana Service, use command :-
```
sudo systemctl enable grafana-server
```
```
sudo systemctl start grafana-server
```
## Step 6: To check Grafana Status, use command :-
```
sudo systemctl status grafana-server
```

## Step 10: Import a Dashboard :-

### To make it easier to view metrics, you can import a pre-configured dashboard follow these steps :-
+ ### Click on the "+" (plus) icon in the left sidebar to open the "Create" menu.

+ ### Select "Dashboard."

+ ### Click on the "Import" dashboard option.

+ ### Enter the dashboard code you want to import (e.g., code 1860).

+ ### Click the "Load" button.

+ ### Select the data source you added (Prometheus) from the dropdown.

+ ### Click on the "Import" button.
+ ### You should now have a Grafana dashboard set up to visualize metrics from Prometheus for **Node Exporter**.
![](https://github.com/sudhajobs0107/Netflix/blob/main/public/assets/node.PNG)

## Now to monitor Jenkins we need a plugin so Go to Manage Jenkins → Plugins → Available Plugins → Install "Prometheus metrics"  → Restart Jenkins. After login back to Jenkins Go to Manage Jenkins → System → Prometheus → Apply and Save.
### Now go back to instance in the prometheus.yml file add as given below :-
```
  - job_name: 'jenkins'
    metrics_path: '/prometheus'
    static_configs:
      - targets: ['<your-jenkins-ip>:8080']
```
### To check the syntex correct or not, use command :-
```
promtool check config /etc/prometheus/prometheus.yml
```


### Now reload the Prometheus configuration without restarting, for this use command :-
```
curl -X POST http://localhost:9090/-/reload
```

### To view metrics, you can import a pre-configured dashboard follow these steps :-
+ ### Click on the "+" (plus) icon in the left sidebar to open the "Create" menu.

+ ### Select "Dashboard."

+ ### Click on the "Import" dashboard option.

+ ### Enter the dashboard code you want to import (e.g., code 9964).

+ ### Click the "Load" button.

+ ### Select the data source you added (Prometheus) from the dropdown.

+ ### Click on the "Import" button.

+ ### Now we will see a Grafana dashboard set up to visualize metrics from Jenkins.

# We've successfully installed and set up Grafana to work with Prometheus for monitoring and visualization.

# Our Netflix Clone Project is completed :-).
___
