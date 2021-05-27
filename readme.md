# login to the Azure
az login

# getting list of subscriptions
- az account list --output table

# Using the specific subscription for demos
az account set --subscription "My Demos"

1. Create an application
2. Create a Docker Image
    - .dockerignore file
    - DockerFile
3. Build the docker Image
    - docker build . -t HelloAKS:0.0.1
4. Create An Azure Container Registry
    - # create the resource group
        az group create -n hello-aks -l westeurope
    - # create the ACR instance
            az acr create -n msitacr \
            -g hello-aks \
            --admin-enabled false \
            --sku Basic
5. # store unique ACR id in ACR_ID
ACR_ID=$(az acr show -n msitacr -g hello-aks --query id -o tsv)

6. Push The Docker Image To ACR
    - # re-tag the existing Docker Image
            docker tag hello-aks:0.0.1 msitacr.azurecr.io/hello-aks:0.0.1
7. # authenticate against ACR
        az acr login -n msitacr
8. # push the docker image to ACR
    docker push msitacr.azurecr.io/hello-aks:0.0.1
9. Create A Kubernetes Cluster
    # create the AKS cluster
        az aks create -n aks-demo \
        -g hello-aks \
        --enable-managed-identity \
        --attach-acr $ACR_ID \
        --node-count 1
10. # Install kubectl
        az aks install-cli
11. # download cluster configuration for kubectl
            az aks get-credentials \
            -n aks-demo \
            -g hello-aks

# verify kubectl context
            kubectl config get-contexts
# prints all cluster contexts

# if kubectl does not point to aks-demo, switch context
        kubectl config use-context aks-demo

12. Create Kubernetes Deployment Manifests
    pod.yml
    service.yml
13. Deploy Your Application 
    # deploy artifacts to AKS
    - kubectl apply -f pod.yml
    - kubectl apply -f service.yml
# wait for service to receive its external IP
    kubectl get svc -w

14. kubectl get pods
15. kubectl get endpoints 
16. get the logs of deployment
kubectl logs hello-aspnetcore-deployment-7c6fcdd9b4-qdf2j


        








    


