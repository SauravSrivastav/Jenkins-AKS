# Service Principle in Azure is UserName and Password for our service thats runs in the background 

Step1: Azure Active Directory -> App registrations -> +New Registration -> Name:jenkins-aks-sp -> Register 

Step2: Take Application (client) ID: 0c3e5b55-16c0-4872-897f-8ff47b1cd8e3: UserName
       Certificate & Secrets: lHaiBC-xZ_qeF9.jF163_Vt4yebt355bH2 :This will be our password(Please make note of this secret as one we will be out of this place it will no longer visible).

Step3:  Manage Jenkins(We will provide Username and Password for Jenkins to talk to Azure).

Step4: Manage Credentials -> Global credentials -> Stores scoped to Jenkins(Global) -> 
       Kind : Username and Password(Application (client) ID: 0c3e5b55-16c0-4872-897f-8ff47b1cd8e3)
       Password: lHaiBC-xZ_qeF9.jF163_Vt4yebt355bH2
       Id(is like a name): jenkins-aks-sp

Step5: Now we will use this Service Principle to grant ACR pull access 
       Access Control -> Add role assignment -> Acr Push/Pull 

Step6: Authenticate with Azure Container Registry from Azure Kubernetes Service
       az aks update -n aksJenkinsAzure -g rgJenkinsAzure --attach-acr acrJenkinsAzure

This integration assigns the AcrPull role to the service principal(myJenkinsServicePrincipal) associated to the AKS Cluster.


