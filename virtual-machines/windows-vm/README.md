🖥️ Deploying a Windows Virtual Machine in Azure

This lab demonstrates how to create and manage a Windows Virtual Machine (VM) in Microsoft Azure using the Azure CLI.

**Step 1: Log in to Azure**
```bash
az login
```
Authenticates your CLI session with Azure. A browser window will open where you’ll sign in using your Microsoft account.

**Step 2: Create a Resource Group**
```bash
az group create --name MyResourceGroup --location eastus
```
Creates a new resource group named MyResourceGroup in the East US region.
All resources for this lab will be placed under this group for easy management.

**Step 3: Create a Virtual Network and Subnet**
```bash
az network vnet create \
  --resource-group MyResourceGroup \
  --name MyVnet \
  --subnet-name MySubnet
```
Sets up a Virtual Network (MyVnet) with a default subnet (MySubnet) where the VM will connect.

**Step 4: Create a Public IP Address**
```bash
az network public-ip create \
  --resource-group MyResourceGroup \
  --name MyPublicIP
```
Creates a public IP address to allow remote access to the VM via RDP (Remote Desktop Protocol).

**Step 5: Create a Network Security Group (NSG)**
```bash
az network nsg create \
  --resource-group MyResourceGroup \
  --name MyNetworkSecurityGroup
```
Creates an NSG that will hold security rules to control inbound and outbound traffic for the VM.

**Step 6: Add an RDP Rule**
```bash
az network nsg rule create \
  --resource-group MyResourceGroup \
  --nsg-name MyNetworkSecurityGroup \
  --name AllowRDP \
  --protocol tcp \
  --priority 1000 \
  --destination-port-range 3389 \
  --access allow
```
Allows inbound RDP traffic (port 3389), which is needed to connect to the Windows VM remotely.

**Step 7: Create a Network Interface**
```bash
az network nic create \
  --resource-group MyResourceGroup \
  --name MyNic \
  --vnet-name MyVnet \
  --subnet MySubnet \
  --network-security-group MyNetworkSecurityGroup \
  --public-ip-address MyPublicIP
```
Links the subnet, NSG, and public IP together through a network interface (MyNic), which will attach to the VM.

**Step 8: Create the Windows Virtual Machine**
```bash
az vm create \
  --resource-group MyResourceGroup \
  --name MyWinVM \
  --image Win2022Datacenter \
  --admin-username azureuser \
  --admin-password P@ssword1234! \
  --nics MyNic
```
Deploys a new Windows Server 2022 Datacenter VM named MyWinVM with the given admin credentials.

⚠️ Use a strong password that meets Azure complexity requirements.

**Step 9: Verify the VM Status**
```bash
az vm get-instance-view \
  --resource-group MyResourceGroup \
  --name MyWinVM \
  --query instanceView.statuses[1]
```
Displays the VM’s current status (e.g., running, stopped, deallocated).

**Step 10: Connect to Your VM**

Use your RDP client and connect to the public IP address:
```bash
az vm show \
  --resource-group MyResourceGroup \
  --name MyWinVM \
  --show-details \
  --query [publicIps] \
  --output tsv
```
Retrieves the VM’s public IP address.
Use this in your Remote Desktop client to log in with your username (azureuser) and password.

**Step 11: Delete Resources (Optional)**
```bash
az group delete --name MyResourceGroup --yes --no-wait
```
Deletes all resources created in this lab to avoid unnecessary Azure charges.
