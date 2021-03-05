---
title: Backend Pool Management
titleSuffix: Azure Load Balancer
description: Começar a aprender como configurar e gerir a piscina de backend de um Equilibrador de Carga Azure
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 01/28/2021
ms.author: allensu
ms.openlocfilehash: 0218bfef66e779a31d999c8d58bc1ce2691f46d4
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179226"
---
# <a name="backend-pool-management"></a>Gestão de piscinas de backend
A piscina de backend é um componente crítico do equilibrador de carga. O pool backend define o grupo de recursos que servirá o tráfego para uma determinada regra de equilíbrio de carga.

Há duas formas de configurar uma piscina de backend:
* Cartão de interface de rede (NIC)
* Combinação de endereço IP e ID de Recursos de Rede Virtual (VNET)

Configure o seu pool de backend por NIC ao utilizar máquinas virtuais existentes e conjuntos de balanças de máquinas virtuais. Este método constrói a ligação mais direta entre o seu recurso e o pool backend. 

Ao pré-afinar o seu pool de backend com uma gama de endereços IP que planeia criar mais tarde máquinas virtuais e conjuntos de balanças de máquinas virtuais, configuure o seu pool de backend por endereço IP e combinação VNET ID.

Você pode configurar piscinas de backend baseadas em IP e NIC para o mesmo balanceador de carga, no entanto, você não pode criar um único pool backend que misture endereços apoiados direcionados por endereços NIC e IP dentro da mesma piscina.

As secções de configuração deste artigo centrar-se-ão em:

* Azure PowerShell
* CLI do Azure
* API REST
* Modelos do Azure Resource Manager 

Estas secções dão uma ideia de como os pools de backend são estruturados para cada opção de configuração.

## <a name="configuring-backend-pool-by-nic"></a>Configurar piscina de backend por NIC
O pool backend é criado como parte da operação do balançador de carga. A propriedade de configuração IP do NIC é usada para adicionar membros de pool backend.

Os exemplos a seguir são focados nas operações de criação e povoamento para o pool backend para destacar este fluxo de trabalho e relação.

  >[!NOTE] 
  >É importante notar que os pools de backend configurados através da interface de rede não podem ser atualizados como parte de uma operação no pool de backend. Qualquer adição ou supressão de recursos backend deve ocorrer na interface de rede do recurso.

### <a name="powershell"></a>PowerShell
Criar uma nova piscina de backend:
 
```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"

$backendPool = 
New-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName  
```

Crie uma nova interface de rede e adicione-a ao pool backend:

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"
$nicname = "myNic"
$location = "eastus"
$vnetname = <your-vnet-name>

$vnet = 
Get-AzVirtualNetwork -Name $vnetname -ResourceGroupName $resourceGroup

$nic = 
New-AzNetworkInterface -ResourceGroupName $resourceGroup -Location $location -Name $nicname -LoadBalancerBackendAddressPool $backendPoolName -Subnet $vnet.Subnets[0]
```

Recupere as informações do pool de backend para o balançador de carga para confirmar que esta interface de rede é adicionada ao pool de backend:

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"

$lb =
Get-AzLoadBalancer -ResourceGroupName $res
Get-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName 
```

Crie uma nova máquina virtual e fixe a interface de rede para colocá-la na piscina de backend:

```azurepowershell-interactive
# Create a username and password for the virtual machine
$cred = Get-Credential

# Create a virtual machine configuration
$vmname = "myVM1"
$vmsize = "Standard_DS1_v2"
$pubname = "MicrosoftWindowsServer"
$nicname = "myNic"
$off = "WindowsServer"
$sku = "2019-Datacenter"
$resourceGroup = "myResourceGroup"
$location = "eastus"

$nic =
Get-AzNetworkInterface -Name $nicname -ResourceGroupName $resourceGroup

$vmConfig = 
New-AzVMConfig -VMName $vmname -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmname -Credential $cred | Set-AzVMSourceImage -PublisherName $pubname -Offer $off -Skus $sku -Version latest | Add-AzVMNetworkInterface -Id $nic.Id
 
# Create a virtual machine using the configuration
$vm1 = New-AzVM -ResourceGroupName $resourceGroup -Zone 1 -Location $location -VM $vmConfig
```

### <a name="cli"></a>CLI
Crie a piscina de backend:

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool 
```

Crie uma nova interface de rede e adicione-a ao pool backend:

```azurecli-interactive
az network nic create \
--resource-group myResourceGroup \
--name myNic \
--vnet-name myVnet \
--subnet mySubnet \
--network-security-group myNetworkSecurityGroup \
--lb-name myLB \
--lb-address-pools myBackEndPool
```

Recupere o pool de backend para confirmar que o endereço IP foi corretamente adicionado:

```azurecli-interactive
az network lb address-pool show \
--resource-group myResourceGroup \
--lb-name myLb \
--name myBackendPool
```

Crie uma nova máquina virtual e fixe a interface de rede para colocá-la na piscina de backend:

```azurecli-interactive
az vm create \
--resource-group myResourceGroup \
--name myVM \
--nics myNic \
--image UbuntuLTS \
--admin-username azureuser \
--generate-ssh-keys
```

### <a name="rest-api"></a>API REST
Crie a piscina de backend:

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/loadBalancers/{load-balancer-name}/backendAddressPools/{backend-pool-name}?api-version=2020-05-01
```

Crie uma interface de rede e adicione-a ao pool de backend que criou através das configurações IP propriedade da interface de rede:

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/networkInterfaces/{nic-name}?api-version=2020-05-01
```

Corpo de pedido JSON:
```json
{
  "properties": {
    "enableAcceleratedNetworking": true,
    "ipConfigurations": [
      {
        "name": "ipconfig1",
        "properties": {
          "subnet": {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}/subnets/{subnet-name}"
          },
          "loadBalancerBackendAddressPools": [
            {
              "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/loadBalancers/{load-balancer-name}/backendAddressPools/{backend-pool-name}"
            }
          ]
        }
      }
    ]
  },
  "location": "eastus"
}
```

Recupere as informações do pool de backend para o balançador de carga para confirmar que esta interface de rede é adicionada ao pool de backend:

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name/providers/Microsoft.Network/loadBalancers/{load-balancer-name/backendAddressPools/{backend-pool-name}?api-version=2020-05-01
```

Crie um VM e anexe o NIC a fazer referência à piscina de backend:

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Compute/virtualMachines/{vm-name}?api-version=2019-12-01
```

Corpo de pedido JSON:
```JSON
{
  "location": "easttus",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_D1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "2016-Datacenter",
        "publisher": "MicrosoftWindowsServer",
        "version": "latest",
        "offer": "WindowsServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Standard_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "myVM",
      "adminPassword": "{your-password}"
    }
  }
}
```

### <a name="resource-manager-template"></a>Modelo do Resource Manager
Siga este [modelo quickstart Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-load-balancer-standard-create/) para implementar um equilibrador de carga e máquinas virtuais e adicione as máquinas virtuais ao pool de backend através da interface de rede.

## <a name="configure-backend-pool-by-ip-address-and-virtual-network"></a>Configure o pool de backend por endereço IP e rede virtual
Em cenários com piscinas de backend pré-povoadas, utilize IP e rede virtual.

Toda a gestão da piscina de backend é feita diretamente no objeto da piscina backend, como realçado nos exemplos abaixo.

### <a name="limitations"></a>Limitações
Um Pool backend configurado por endereço IP tem as seguintes limitações:
  * Só pode ser usado para equilibradores de carga standard
  * Limite de 100 endereços IP na piscina de backend
  * Os recursos de backend devem estar na mesma rede virtual que o equilibrador de carga
  * Um balanceador de carga com backend pool baseado em IP não pode funcionar como um serviço de ligação privada
  * Esta funcionalidade não é suportada atualmente no portal Azure
  * Os contentores ACI não são suportados atualmente por esta funcionalidade
  * Os balançadores de carga ou serviços frontados por balançadores de carga não podem ser colocados no pool de backend do equilibrador de carga
  * As regras nat de entrada não podem ser especificadas por endereço IP

### <a name="powershell"></a>PowerShell
Criar uma nova piscina de backend:

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"
$vnetName = "myVnet"
$location = "eastus"
$nicName = "myNic"

$backendPool = New-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -Name $backendPoolName  
```

Atualizar o backend pool com um novo IP da rede virtual existente:
 
```azurepowershell-interactive
$virtualNetwork = 
Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroup 
 
$ip1 = New-AzLoadBalancerBackendAddressConfig -IpAddress "10.0.0.5" -Name "TestVNetRef" -VirtualNetwork $virtualNetwork  
 
$backendPool.LoadBalancerBackendAddresses.Add($ip1) 

Set-AzLoadBalancerBackendAddressPool -InputObject $backendPool
```

Recupere as informações do pool de backend para o balançador de carga para confirmar que os endereços de backend são adicionados ao pool de backend:

```azurepowershell-interactive
Get-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -Name $backendPoolName 
```
Crie uma interface de rede e adicione-a ao pool de backend. Desaponuse o endereço IP num dos endereços de backend:

```azurepowershell-interactive
$nic = 
New-AzNetworkInterface -ResourceGroupName $resourceGroup -Location $location -Name $nicName -PrivateIpAddress 10.0.0.4 -Subnet $virtualNetwork.Subnets[0]
```

Crie um VM e anexe o NIC com um endereço IP na piscina de backend:
```azurepowershell-interactive
# Create a username and password for the virtual machine
$cred = Get-Credential

# Create a virtual machine configuration
$vmname = "myVM1"
$vmsize = "Standard_DS1_v2"
$pubname = "MicrosoftWindowsServer"
$nicname = "myNic"
$off = "WindowsServer"
$sku = "2019-Datacenter"
$resourceGroup = "myResourceGroup"
$location = "eastus"

$nic =
Get-AzNetworkInterface -Name $nicname -ResourceGroupName $resourceGroup

$vmConfig = 
New-AzVMConfig -VMName $vmname -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmname -Credential $cred | Set-AzVMSourceImage -PublisherName $pubname -Offer $off -Skus $sku -Version latest | Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine using the configuration
$vm1 = New-AzVM -ResourceGroupName $resourceGroup -Zone 1 -Location $location -VM $vmConfig
```

### <a name="cli"></a>CLI
Utilizando o CLI, pode povoar o pool de backend através de parâmetros de linha de comando ou através de um ficheiro de configuração JSON. 

Criar e povoar a piscina de backend através dos parâmetros da linha de comando:

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool \
--vnet {VNET resource ID} \
--backend-address name=addr1 ip-address=10.0.0.4 \
--backend-address name=addr2 ip-address=10.0.0.5
```

Criar e povoar o Backend Pool através do ficheiro de configuração JSON:

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool \
--vnet {VNET resource ID} \
--backend-address-config-file @config_file.json
```

Ficheiro de configuração JSON:
```JSON
        [
          {
            "name": "address1",
            "virtualNetwork": "/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}",
            "ipAddress": "10.0.0.4"
          },
          {
            "name": "address2",
            "virtualNetwork": "/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}",
            "ipAddress": "10.0.0.5"
          }
        ]
```

Recupere as informações do pool de backend para o balançador de carga para confirmar que os endereços de backend são adicionados ao pool de backend:

```azurecli-interactive
az network lb address-pool show \
--resource-group myResourceGroup \
--lb-name MyLb \
--name MyBackendPool
```

Crie uma interface de rede e adicione-a ao pool de backend. Desaponuse o endereço IP num dos endereços de backend:

```azurecli-interactive
az network nic create \
  --resource-group myResourceGroup \
  --name myNic \
  --vnet-name myVnet \
  --subnet mySubnet \
  --network-security-group myNetworkSecurityGroup \
  --lb-name myLB \
  --private-ip-address 10.0.0.4
```

Crie um VM e anexe o NIC com um endereço IP na piscina de backend:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --nics myNic \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

### <a name="rest-api"></a>API REST

Crie a piscina de backend e defina os endereços de backend através de um pedido de pool PUT backend. Configure os endereços de backend no corpo JSON do pedido PUT:

* Nome do endereço
* Endereço IP
* ID de rede virtual 

```
PUT https://management.azure.com/subscriptions/subid/resourceGroups/testrg/providers/Microsoft.Network/loadBalancers/lb/backendAddressPools/backend?api-version=2020-05-01
```

Corpo de Pedido JSON:
```JSON
{
  "properties": {
    "loadBalancerBackendAddresses": [
      {
        "name": "address1",
        "properties": {
          "virtualNetwork": {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}"
          },
          "ipAddress": "10.0.0.4"
        }
      },
      {
        "name": "address2",
        "properties": {
          "virtualNetwork": {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}"
          },
          "ipAddress": "10.0.0.5"
        }
      }
    ]
  }
}
```

Recupere as informações do pool de backend para o balançador de carga para confirmar que os endereços de backend são adicionados ao pool de backend:
```
GET https://management.azure.com/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/loadBalancers/{load-balancer-name}/backendAddressPools/{backend-pool-name}?api-version=2020-05-01
```

Crie uma interface de rede e adicione-a ao pool de backend. Desaponuse o endereço IP num dos endereços de backend:
```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/networkInterfaces/{nic-name}?api-version=2020-05-01
```

Corpo de Pedido JSON:
```JSON
{
  "properties": {
    "enableAcceleratedNetworking": true,
    "ipConfigurations": [
      {
        "name": "ipconfig1",
        "properties": {
          "privateIPAddress": "10.0.0.4",
          "subnet": {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}/subnets/{subnet-name}"
          }
        }
      }
    ]
  },
  "location": "eastus"
}
```

Crie um VM e anexe o NIC com um endereço IP na piscina de backend:

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Compute/virtualMachines/{vm-name}?api-version=2019-12-01
```

Corpo de Pedido JSON:
```JSON
{
  "location": "eastus",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_D1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "2016-Datacenter",
        "publisher": "MicrosoftWindowsServer",
        "version": "latest",
        "offer": "WindowsServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Standard_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "myVM",
      "adminPassword": "{your-password}"
    }
  }
}
```
  
## <a name="next-steps"></a>Passos seguintes
Neste artigo, você aprendeu sobre a gestão do pool backend balancer Azure Load balancer e como configurar um pool de backend por endereço IP e rede virtual.

Saiba mais sobre [o Azure Load Balancer](load-balancer-overview.md).
