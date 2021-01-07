# Step1: Create Azure Service principal
# Step2: Deploy an Ubuntu server from the marketplace with Jenkins installed
# Step3: Create resources for Azure container registry ACR and AKS cluster
# Step4: Configure the Jenkins server to talk to the Docker hub, ACR & AKS
# Step5: Install Docker and KubeCtl on the Ubuntu server
# Step6: Connect the AKS cluster on the Ubuntu server
# Step7: Connect the GitHub from the Jenkins server
# Step8: Set up a WebHook to automate builds when code is pushed into GitHub
# Step9: Enabled access between Jenkins and Docker on the Ubuntu server
# Step10: Parameterise the Kubernetes deployment scripts (yaml) to ensure that the latest images is pushed into   the container registry are deployed to the Kubernetes cluster.
# Step11: Verify that a code push in GitHub triggered a successful Docker build and push as well as a successful Kubernetes deployment of the latest image tags. CI/CD pipeline working perfectly from GitHub to deployment on Kubernetes.
# Step12: Imported the GitHub Repo into Azure Git Repo.
# Step13: Set up another Jenkins Build to use Azure Repo as SCM source
# Step14: Created a web-hook on Azure DevOps to notify Jenkins for a build whenever there is a code push.
# Step15: Verified that a code push in Azure Repo triggered a successful Docker build and push as well as a successful Kubernetes deployment of the latest image tags. CI/CD pipeline working perfectly from GitHub to deployment on Kubernetes



docker run -it --rm -v ${PWD}:/work -w /work --entrypoint /bin/sh mcr.microsoft.com/azure-cli:2.6.0

export rg=rgJenkinsAzure
export location=eastus
export acr=acrJenkinsAzure
export name=aksJenkinsAzure
export acrPull=Contributor

az login

az account set \
  --subscription a2ca6b0c-2592-420a-be30-b1cbe1da7646

# Create a resource group $rg on a specific location $location (for example eastus) which will contain the Azure services we need 
az group create \
  -l $location \
  -n $rg

az ad sp create-for-rbac \
  --skip-assignment \
  --name myJenkinsServicePrincipal
{
  "appId": "d5d49d6c-666d-4748-8a01-1f77489ac793",
  "displayName": "myJenkinsServicePrincipal",
  "name": "http://myJenkinsServicePrincipal",
  "password": "qlRWvrl3eTWC8oY.G-AbRs.XT6iNoZ9NkL",
  "tenant": "214afa4d-d9b0-4cc4-9497-06abd7cbc5ea"
}


# Create an ACR registry $acr
az acr create \
  -n $acr \
  -g $rg \
  -l $location \
  --sku Basic




# Setup of the AKS cluster
latestK8sVersion=$(az aks get-versions -l $location --query 'orchestrators[-1].orchestratorVersion' -o tsv)
az aks create \
    -l $location \
    -n $name \
    -g $rg \
    --generate-ssh-keys \
    --enable-addons monitoring \
    -k 1.18.6 \
    --node-count 1 \
    --node-vm-size Standard_B2s \
    --service-principal d5d49d6c-666d-4748-8a01-1f77489ac793 \
    --client-secret qlRWvrl3eTWC8oY.G-AbRs.XT6iNoZ9NkL

# Note: SH key files '/home/saurav/.ssh/id_rsa' and '/home/saurav/.ssh/id_rsa.pub' have been generated under ~/.ssh to allow SSH access to the VM. If usingmachines without permanent storage like Azure Cloud Shell without an attached file share, back up your keys to a safe location.

# Once created (the creation could take ~10 min), get the credentials to interact with your AKS cluster.


az aks get-credentials \
  --resource-group $rg \
  --name $name


AKS_ID=$(az aks show -n $name -g $rg --query "servicePrincipalProfile.clientId" --output tsv)
echo $AKS_ID

ACR_ID=$(az acr show -n $acr -g $rg --query "id" --output tsv)
echo $ACR_ID

az role assignment create \
  --assignee $AKS_ID \
  --role $acrPull \
  --scope $ACR_ID

az aks use-dev-spaces \
  -n $name \
  -g $rg 

# optional 
# curl -L https://aka.ms/get-azds-mac | bash
az aks update --name $name \
    --resource-group $rg \
    --attach-acr $ACR_ID