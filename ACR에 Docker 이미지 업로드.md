### 1. Docker 설치 ###
```
apt-get install docker.io -y
```

### 2. Kubectl 설치 ###
```
az aks install-cli
```

### 3. ACR 설치 ###
```
az acr create --resource-group user15-rg --name user15acrdemo1 --sku Basic
```

### ACR 리스트 보기 ###
```
az acr list --resource-group user15-rg --query "[].{acrLoginServer:loginServer}" --output table
```

### 4. AKS 생성 ###
```
az aks create     --resource-group user15-rg     --name user15AKS     --node-count 2     --generate-ssh-keys     --attach-acr user15acrdemo1
```

### 5. 컨테이너 이미지 가져오기 ###
```
docker pull httpd
```

### 6. 컨테니어 실행 ###
```
docker run -d --name 컨테이너이름 -p 80:80 httpd
```
ex) docker run -d --name user15web -p 80:80 httpd

### 7. 컨테이너 index.html 파일 수정 ###
```
docker exec -it 컨테이너ID /bin/bash
```
ex) docker exec -it 96794df8f52f /bin/bash

```
echo "<html>" > index.html
echo "<body>" >> index.html
echo "Azure Region1 <br>" >> index.html
echo "<img src=\"https://user15cdn.azureedge.net/img/chatGPT.png\">" >> index.html
echo "</body>" >> index.html
echo "</html>" >> index.html
```

컨테이너ID는 docker ps 명령어로 확인

### 8. 컨테이너 이미지 생성(Tag 생성) ###
```
docker stop 컨테이너ID      => 컨테이너 중지
docker commit -a "코멘트" 컨테이너ID  생성할컨테이너
```
ex) docker commit -a "New Httpd" 96794df8f52f user15acrdemo1.azurecr.io/httpd:v1

### 9. ACR 로그인 ###
```
az acr login --name user15acrdemo1
```

### 10. 도커 이미지 ACR에 업로드 ###
```
docker push user15acrdemo1.azurecr.io/httpd:v1
```
