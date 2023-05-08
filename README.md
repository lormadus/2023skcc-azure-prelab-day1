
### 수업자료 ###
https://bit.ly/41Mqk44


### 과제 ###
https://bit.ly/3nDQHL3


### Azure VMSS Terraform 참고용 소스 코드 ###
강사 자료<br>
https://github.com/lormadus/azure-vmss-new

MS 공식 홈페이지 자료 <br>
https://learn.microsoft.com/ko-kr/azure/developer/terraform/create-vm-scaleset-network-disks-hcl


### Azure CLI 설치 ###
```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

### 테라폼 설치 ###
```
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform
```
