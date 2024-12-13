# Lab Project Assignment #2: Building a Cloud-Native App for Best Buy
Donna Ha - 041174159

## Updated Application Architecture
![application architecture diagram](Pictures/A2_FullStackAPP2.drawio.png)

## Application and Architecture Explanation

## Deployment Instructions (Azure)
Prerequisite: Install kubectl, az login (to use azure)

How to deploy the application in a Kubernetes cluster:

### Step 1: Clone the repository
  1. Fork and clone this repository. It includes all the necessary deployment files for this application


### Step 2: Set up the AKS Cluster
  1. Log in to your Azure Portal
  2. Create a resource group in your target region, give it a name
  3. In the portal, search for **Kubernetes services** and click **Create**
  4. Choose your desired configurations
     - Choose the resource group created in the previous step
     - If you want a basic set up, follow the instructions in [Lab 6](https://github.com/ramymohamed10/Lab6_24F_CST8915) offered by Professor Ramy
  5. Use Azure CLI to log into VScode terminal (or alternative)
      - In your newly created Kubernetes service, click on connect > Azure CLI > copy and paste command
      ```
      az aks get-credentials --resource-group <Resource_Group_Name> --name <ckuster_name>
      ```

### Step 3: Set up the AI Backing Services
  1. Create an Azure OpenAI Service Instance
     - Open Azure Portal
     - Search for Azure OpenAI and click create
     - Choose the region of your choice (East US is recommended for capacity in my case) for GPT-4 and DALL-E
     - Choose resource group and pricing tier
     - Review + Create
  2. Deploy GPT-4 and DALL-E
     - Go to the created Azure OpenAI resource
     - Go to Model Deployments and click Add Deployment
     - Choose GPT-4 model and deploy it with the wanted configurations
     - Repeat for Dall-e, ensure that they are under the same region for easy use of endpoint
     - Once deployed, go to home, view JSON, and copy the key and the endpoint (the endpoint should be the same for both models as they are under the same region)
     - Encode the key by doing the following in the terminal (after you have logged in with Azure CLI)
       ```
       echo -n "<your-api-key>" | base64
       ```

  3. In the forked and cloned repository, go to the Deployments folder open secrets.yaml
     - Replace the **OPENAI_API_KEY** placeholder with the Base64-encoded value of the **API_KEY**  
  4. Go the **aps-all-in-one.yaml** file
     - Replace the placeholders with the configurations you retrieved:
     - `AZURE_OPENAI_DEPLOYMENT_NAME`: Enter the deployment name for GPT-4.
     - `AZURE_OPENAI_ENDPOINT`: Enter the endpoint URL for the GPT-4 deployment.
     - `AZURE_OPENAI_DALLE_ENDPOINT`: Enter the endpoint URL for the DALL-E 3 deployment.
     - `AZURE_OPENAI_DALLE_DEPLOYMENT_NAME`: Enter the deployment name for DALL-E 3.

   Example configuration in the YAML file:
   ```yaml
   - name: AZURE_OPENAI_API_VERSION
     value: "2024-07-01-preview"
   - name: AZURE_OPENAI_DEPLOYMENT_NAME
     value: "gpt-4-deployment"
   - name: AZURE_OPENAI_ENDPOINT
     value: "https://<your-openai-resource-name>.openai.azure.com/"
   - name: AZURE_OPENAI_DALLE_ENDPOINT
     value: "https://<your-openai-resource-name>.openai.azure.com/"
   - name: AZURE_OPENAI_DALLE_DEPLOYMENT_NAME
     value: "dalle-3-deployment"
   ```
      
### Step 4: Deploy the ConfigMaps and Secrets
- Deploy the ConfigMap for RabbitMQ Plugins:
   ```bash
   kubectl apply -f config-maps.yaml
   ```
- Create and Deploy the Secret for OpenAI API:  
   ```bash
   kubectl apply -f secrets.yaml
   ```
- Verify:
   ```bash
   kubectl get configmaps
   kubectl get secrets
   ```
      
### Step 5: Deploy the Application
   ```bash
   kubectl apply -f aps-all-in-one.yaml
   ```

- Check Pods and Services:
   ```bash
   kubectl get pods
   kubectl get services
   ```
- Go to the kubernetes service on Azure, Go to Workloads to check on the status of all the pods
- Once all are ready, go to the **Services and ingresses**, and click on the endpoint for store-front, and store-admin to access the application!



## Table of Microservice Repositories
| **Service**         | **Repository Link**                       |
|---------------------|-------------------------------------------|
| Store-Front         | `https://github.com/Donna-Smee/store-front-a2` |
| Store-Admin         | `https://github.com/Donna-Smee/store-admin-a2` |
| Order-Service       | `https://github.com/Donna-Smee/order-service-a2` |
| Product-Service     | `https://github.com/Donna-Smee/product-service-a2` |
| Makeline-Service    | `https://github.com/Donna-Smee/makeline-service-a2` |
| Ai-Service          | `https://github.com/Donna-Smee/ai-service-a2` |


## Table of Docker Images
| **Service**         | **Docker Image Link**                     |
|---------------------|-------------------------------------------|
| store-front-a2         | `https://hub.docker.com/r/dailydonuts/store-front-a2` |
| store-admin-a2         | `https://hub.docker.com/r/dailydonuts/store-admin-a2` |
| order-service-a2       | `https://hub.docker.com/r/dailydonuts/order-service-a2` |
| product-service-a2     | `https://hub.docker.com/r/dailydonuts/product-service-a2` |
| makeline-service-a2    | `https://hub.docker.com/r/dailydonuts/makeline-service-a2` |
| ai-service-a2          | `https://hub.docker.com/r/dailydonuts/ai-service-a2` |


## Any issues or limitations in the implementation




