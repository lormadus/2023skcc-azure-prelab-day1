아래의 예제는 Azure Kubernetes Service (AKS)에서 Azure Container Registry (ACR)에 있는 도커 이미지를 LoadBalancer를 사용하여 서비스하는 과정을 설명합니다.

Azure CLI를 설치하고 로그인합니다.
bash
Copy code
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
az login
ACR과 AKS 리소스 그룹 및 클러스터를 생성합니다.
bash
Copy code
# 변수 설정
ACR_NAME=myACRRegistry
AKS_RESOURCE_GROUP=myAKSResourceGroup
AKS_CLUSTER_NAME=myAKSCluster
LOCATION=centralus

# 리소스 그룹 생성
az group create --name $AKS_RESOURCE_GROUP --location $LOCATION

# ACR 생성
az acr create --resource-group $AKS_RESOURCE_GROUP --name $ACR_NAME --sku Basic --location $LOCATION

# AKS 클러스터 생성
az aks create --resource-group $AKS_RESOURCE_GROUP --name $AKS_CLUSTER_NAME --node-count 1 --generate-ssh-keys --attach-acr $ACR_NAME
ACR에 도커 이미지를 푸시합니다.
bash
Copy code
# 로컬 도커 이미지 생성 (예: my-app)
docker build -t my-app .

# ACR 로그인
az acr login --name $ACR_NAME

# 이미지 태그
ACR_REGISTRY=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
docker tag my-app $ACR_REGISTRY/my-app:v1

# 이미지 푸시
docker push $ACR_REGISTRY/my-app:v1
AKS 클러스터에 연결합니다.
bash
Copy code
az aks get-credentials --resource-group $AKS_RESOURCE_GROUP --name $AKS_CLUSTER_NAME
Kubernetes Deployment 및 LoadBalancer 서비스 생성합니다.
my-app-deployment.yaml 파일을 생성하고 다음 내용을 입력합니다.

yaml
Copy code
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: <YOUR_ACR_REGISTRY>/my-app:v1
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: my-app
spec:
  type: LoadBalancer
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
<YOUR_ACR_REGISTRY>를 3번 단계에서 얻은 ACR 주소로 변경하세요.

생성한 YAML 파일을 적용합니다.
bash
Copy code
kubectl apply -f my-app-deployment.yaml
LoadBalancer의 IP 주소를 확인합니다.
bash
Copy code
kubectl get svc my-app
EXTERNAL-IP 열에서 할당된 IP 주소를 확인하십시오. 이 IP 주소를 사용하여 서비스에 액세스할 수 있습니다.
