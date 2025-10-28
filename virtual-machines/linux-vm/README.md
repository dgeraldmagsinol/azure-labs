🐧 Linux Virtual Machine Lab
🧭 Objective

Deploy and manage a Linux Virtual Machine (Ubuntu) in Azure using the Microsoft Learn sandbox and Azure CLI.

🧱 Step-by-Step Guide

**1️⃣ Login to Azure**
```bash
az login
```
Authenticates your CLI session with Azure.

**2️⃣ Set your subscription (if multiple)**
```bash
az account set --subscription "Your Subscription Name"
```
Specifies which Azure subscription to use.

**3️⃣ Create a resource group**
```bash
az group create --name linux-lab-rg --location eastus
```
Creates a new resource group named linux-lab-rg in the East US region.
(Resource groups organize and manage related Azure resources.)

**4️⃣ Create a virtual network and subnet**
```bash
az network vnet create \
  --resource-group linux-lab-rg \
  --name linux-vnet \
  --subnet-name linux-subnet
```
Creates a virtual network (linux-vnet) and a subnet (linux-subnet) for your VM to connect to.

**5️⃣ Create a public IP address**
```bash
az network public-ip create \
  --resource-group linux-lab-rg \
  --name linux-vm-ip \
  --sku Basic \
  --allocation-method Dynamic
```
Creates a dynamic public IP for your Linux VM.

**6️⃣ Create a network security group (NSG)**
```bash
az network nsg create \
  --resource-group linux-lab-rg \
  --name linux-nsg
```
The NSG acts as a firewall to control inbound and outbound traffic.

**7️⃣ Allow SSH access**
```bash
az network nsg rule create \
  --resource-group linux-lab-rg \
  --nsg-name linux-nsg \
  --name allow-ssh \
  --protocol tcp \
  --priority 1000 \
  --destination-port-range 22 \
  --access allow
```
Opens port 22 so you can connect to the VM via SSH.

**8️⃣ Create a network interface (NIC)**
```bash
az network nic create \
  --resource-group linux-lab-rg \
  --name linux-nic \
  --vnet-name linux-vnet \
  --subnet linux-subnet \
  --network-security-group linux-nsg \
  --public-ip-address linux-vm-ip
```
Connects the virtual network, subnet, and security group together.

**9️⃣ Create the Linux VM**
```bash
az vm create \
  --resource-group linux-lab-rg \
  --name linux-vm \
  --image Ubuntu2204 \
  --admin-username azureuser \
  --authentication-type ssh \
  --generate-ssh-keys \
  --nics linux-nic
```
Creates a Linux VM using the Ubuntu 22.04 image and automatically generates SSH keys.

**🔟 Verify the VM is running**
```bash
az vm show --name linux-vm --resource-group linux-lab-rg --show-details --output table
```
Displays details like IP address and power state.

**1️⃣1️⃣ Connect to the VM via SSH**
```bash
ssh azureuser@<public-ip-address>
```

**1️⃣2️⃣ (Optional) Stop or delete the VM**
```bash
# Stop VM
az vm stop --name linux-vm --resource-group linux-lab-rg

# Delete VM and related resources
az group delete --name linux-lab-rg --yes --no-wait
```
Stops billing or cleans up your sandbox when you’re done.
