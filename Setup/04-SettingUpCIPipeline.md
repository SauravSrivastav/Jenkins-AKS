Step1: Go to Jenkins Main Page

Step2: New Item
       Enter an item name: aks_jenkins_cicd

Step3: Freestyle Project 

Step4: GitHub project
       
Step5: Source Code Management -Git : https://github.com/SauravSrivastav/AKS-GA
       Repository URL: https://github.com/SauravSrivastav/AKS-GA.git(add .git)  

Step6: Build Triggers
       GitHub hook trigger for GITScm polling(for any code changes this will get triggered)

Step7: Build Environment
       Use secret text(s) or file(s)
       Bindings -> Add Username and Password(separated)
Setting Up Enviornment Variable: 
       Username Variable:ACR_USER
       Password Variable:ACR_PASS
       Specific credentials : Application (client) ID: 0c3e5b55-16c0-4872-897f-8ff47b1cd8e3

Step8: Build -> Add Build Step 
       Execute Shell (Shells on the Jenkins)

       Command: 
       # Specify the name of the image
       WEBAPP=${ACR_SERVER_NAME}/webapp:tag${BUILD_NUMBER} (Login Server: acrjenkinsazure.azurecr.io)-> For this we will create env variable.

Step9: Create Env variable: 
       Jenkins -> Manage Jenkins -> Configure System -> Global Properties ->Environment variables
       Name: ACR_SERVER_NAME
       Value: acrjenkinsazure.azurecr.io


Sudo apt install docker.io
