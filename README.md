# Spring Boot Shopping Cart Web App

## About

This is a demo project for practicing Spring + Thymeleaf. The idea was to build some basic shopping cart web app.

It was made using **Spring Boot**, **Spring Security**, **Thymeleaf**, **Spring Data JPA**, **Spring Data REST and Docker**. 
Database is in memory **H2**.

There is a login and registration functionality included.

Users can shop for products. Each user has his own shopping cart (session functionality).
Checkout is transactional.

## Configuration

### Configuration Files

Folder **src/resources/** contains config files for **shopping-cart** Spring Boot application.

* **src/resources/application.properties** - main configuration file. Here it is possible to change admin username/password,
as well as change the port number.

## How to run

There are several ways to run the application. You can run it from the command line with included Maven Wrapper, Maven or Docker. 

Once the app starts, go to the web browser and visit `http://localhost:8070/home`

Admin username: **admin**

Admin password: **admin**

User username: **user**

User password: **password**

### Maven Wrapper

#### Using the Maven Plugin

Go to the root folder of the application and type:
```bash
$ chmod +x scripts/mvnw
$ scripts/mvnw spring-boot:run
```

#### Using Executable Jar

Or you can build the JAR file with 
```bash
$ scripts/mvnw clean package
``` 

Then you can run the JAR file:
```bash
$ java -jar target/shopping-cart-0.0.1-SNAPSHOT.jar
```

### Maven

Open a terminal and run the following commands to ensure that you have valid versions of Java and Maven installed:

```bash
$ java -version
java version "1.8.0_102"
Java(TM) SE Runtime Environment (build 1.8.0_102-b14)
Java HotSpot(TM) 64-Bit Server VM (build 25.102-b14, mixed mode)
```

```bash
$ mvn -v
Apache Maven 3.3.9 (bb52d8502b132ec0a5a3f4c09453c07478323dc5; 2015-11-10T16:41:47+00:00)
Maven home: /usr/local/Cellar/maven/3.3.9/libexec
Java version: 1.8.0_102, vendor: Oracle Corporation
```

#### Using the Maven Plugin

The Spring Boot Maven plugin includes a run goal that can be used to quickly compile and run your application. 
Applications run in an exploded form, as they do in your IDE. 
The following example shows a typical Maven command to run a Spring Boot application:
 
```bash
$ mvn spring-boot:run
``` 

#### Using Executable Jar

To create an executable jar run:

```bash
$ mvn clean package
``` 

To run that application, use the java -jar command, as follows:

```bash
$ java -jar target/shopping-cart-0.0.1-SNAPSHOT.jar
```

To exit the application, press **ctrl-c**.

### Docker

It is possible to run **shopping-cart** using Docker:

Build Docker image:
```bash
$ mvn clean package
$ docker build -t shopping-cart:dev -f docker/Dockerfile .
```

Run Docker container:
```bash
$ docker run --rm -i -p 8070:8070 \
      --name shopping-cart \
      shopping-cart:dev
```

##### Helper script

It is possible to run all of the above with helper script:

```bash
$ chmod +x scripts/run_docker.sh
$ scripts/run_docker.sh
```

## Docker 

Folder **docker** contains:

* **docker/shopping-cart/Dockerfile** - Docker build file for executing shopping-cart Docker image. 
Instructions to build artifacts, copy build artifacts to docker image and then run app on proper port with proper configuration file.

## Util Scripts

* **scripts/run_docker.sh.sh** - util script for running shopping-cart Docker container using **docker/Dockerfile**

## Tests

Tests can be run by executing following command from the root of the project:

```bash
$ mvn test
```

## Helper Tools

### HAL REST Browser

Go to the web browser and visit `http://localhost:8070/`

You will need to be authenticated to be able to see this page.

### H2 Database web interface

Go to the web browser and visit `http://localhost:8070/h2-console`

In field **JDBC URL** put 
```
jdbc:h2:mem:shopping_cart_db
```

In `/src/main/resources/application.properties` file it is possible to change both
web interface url path, as well as the datasource url.


-----------------------


Set up a full CI/CD pipeline that builds a Docker image, runs tests, and deploys to AKS:
----------------------------------------------------------------------------------------

# 🛒 Ekart App – CI/CD with GitHub Actions and AKS

A sample e-commerce backend service built in Java, containerized with Docker, and deployed to Azure Kubernetes Service (AKS) using a fully automated GitHub Actions pipeline.

---

## 📦 Tech Stack

- **Java 17** – Backend logic using Maven
- **Docker** – Containerized application
- **Azure Kubernetes Service (AKS)** – Container orchestration
- **GitHub Actions** – CI/CD automation
- **Docker Hub** – Image registry

---

## 🚀 Features

- Full CI/CD pipeline with GitHub Actions
- Container build and push to Docker Hub
- Seamless AKS deployment on push to `main` branch
- LoadBalancer service for public access

---

## 📂 Project Structure

       EKART
         |---.github/workflows/ci_cd.yaml
         |---.mvn
         |---docker/Dockerfile
         |---src/main
         |---deplpoyment.yml
         |---service.yml



---

## 🔁 CI/CD Pipeline – `.github/workflows/main.yml`

### Triggers
- On push or PR to `main`

### Workflow Steps
1. Checkout code
2. Set up Java
3. Build with Maven
4. Docker build and push to Docker Hub
5. Authenticate to Azure using Service Principal
6. Get AKS credentials
![Image](https://github.com/user-attachments/assets/1277d0f3-56ec-4891-88fc-20f24ae1a05a)

![Image](https://github.com/user-attachments/assets/b99f98b8-ca5f-4cf0-81ad-e14940562451)


7. Deploy updated containers to AKS using `kubectl`

---

## ⚙️ Deployment

### 1. Clone this repository

```bash
git clone https://github.com/<your-username>/ekart-app-github-actions.git
cd ekart-app-github-actions
```

=> To deploy to AKS using az login with GitHub Actions, you cannot use a system-assigned managed identity directly.

Instead, you should use a Service Principal and securely store its credentials in GitHub Secrets.

Use Service Principal for AKS Auth
1. Create Service Principal
Run this on your local machine or Cloud Shell:
```
az ad sp create-for-rbac --name github-aks-deployer \
  --role contributor \
  --scopes /subscriptions/<your-subscription-id>/resourceGroups/aks-rg \
  --sdk-auth
```
and it's output is as folllows and store the complete json output to secrets 
```
{
  "clientId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "clientSecret": "your-client-secret",
  "subscriptionId": "your-subscription-id",
  "tenantId": "your-tenant-id",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "clientCertificate": null,
  "clientCertificatePassword": null,
  "useMSI": false,
  "authenticationType": "client_secret"
}

```
2. Store the output in GitHub Secrets
Create a new GitHub Secret:

Name: AZURE_CREDENTIALS

Value: Paste the full JSON output from above

🔁 Updated GitHub Actions Workflow (Using Service Principal)
Replace the Azure login step in your workflow with:

```
      - name: Azure Login
        uses: azure/login@v1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

```          
This will let you:

-Authenticate securely to Azure

-Get AKS credentials

-Deploy using kubectl


2. Configure GitHub Secrets
```
Secret Name	              Description

DOCKER_USERNAME   -Docker Hub username

DOCKER_PASSWORD	-Docker Hub password or access token

AZURE_CREDENTIALS	-Output of az ad sp create-for-rbac --sdk-auth
```



3. Push to main to trigger deployment
```
git add .
git commit -m "Initial deployment"
git push origin main
```

🌐 Access Your App
Once deployed:
```
kubectl get svc ekart-service
```
![Image](https://github.com/user-attachments/assets/7f74c441-591a-4c0f-ace9-749514b7ef55)

```
Visit your app at http://<EXTERNAL-IP>
```
![Image](https://github.com/user-attachments/assets/d6a80709-1cad-4d06-bb99-a05cb3e6b62c)



AZURE dashboard refernces images:
----------------------------------

![Image](https://github.com/user-attachments/assets/e22cb1fe-ac9b-4a6f-a6b7-f77474773660)

![Image](https://github.com/user-attachments/assets/4c8ebb69-9ec2-445c-901e-a0bb9608b91c)

![Image](https://github.com/user-attachments/assets/a14a874e-e2b3-4204-9ab1-9eaef78bf056)

![Image](https://github.com/user-attachments/assets/0d6bde77-9427-45f0-a19c-23103d31770e)

![Image](https://github.com/user-attachments/assets/c0e47f82-16c9-4266-a703-db5409a7d503)
