---
title: Backend Pool Management
description: Guia para configurar a Piscina de Backend de um Equilibrador de Carga
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: overview
ms.date: 07/06/2020
ms.author: errobin
ms.openlocfilehash: 6d9700e134a9e3d6c53524d15c8b3503cf5773c7
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050190"
---
# <a name="backend-pool-management"></a>Backend Pool Management
O Backend Pool é um componente fundamental do Balanceador de Carga, que define o grupo de recursos computacional que servirá o tráfego para uma determinada regra de equilíbrio de carga. Ao configurar corretamente uma Piscina de Backend, terá definido um grupo de máquinas elegíveis para servir o tráfego. Existem duas formas de configurar um Pool backend, por Cartão interface de rede (NIC) e por um endereço IP combinado e ID de Recursos de Rede Virtual (VNET). 

Na maioria dos cenários que envolvem máquinas virtuais e conjuntos de balanças de máquinas virtuais, recomenda-se configurar o seu Pool backend por NIC, uma vez que este método constrói a ligação mais direta entre o seu recurso e o Backend Pool. Para cenários que envolvam contentores e Kubernetes Pods, que não possuam um NIC ou para pré-alocção de uma série de endereços IP para recursos backend, pode configurar o seu Pool backend por ip address e combinação VNET ID.

Ao configurar por NIC ou IP Address e VNET ID através do Portal, o UI irá acompanhá-lo através de cada passo e todas as atualizações de configuração serão manuseadas no backend. As secções de configuração deste artigo centrar-se-ão nos modelos Azure PowerShell, CLI, REST API e ARM para dar uma ideia de como as Piscinas backend são estruturadas para cada opção de configuração.

## <a name="configuring-backend-pool-by-nic"></a>Configurar pool backend por NIC
Ao configurar um Backend Pool por NIC, é importante ter em mente que o Backend Pool é criado como parte da operação do Balancer de Carga e os membros são adicionados ao Pool backend como parte da propriedade de configuração IP da sua Interface de Rede durante a operação Interface de Rede. Os exemplos a seguir são focados nas operações de criação e povoamento para o Backend Pool para destacar este fluxo de trabalho e relação.

  >[!NOTE] 
  >É importante notar que os Pools de Backend configurados via Interface de Rede não podem ser atualizados como parte de uma operação no Backend Pool. Qualquer adição ou supressão de recursos backend deve ocorrer na Interface de Rede do recurso.

### <a name="powershell"></a>PowerShell
Criar um novo Backend Pool: 
```powershell
$backendPool = New-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup   -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName  
```

Crie uma nova Interface de Rede e adicione-a ao Pool Backend:
```powershell
$nic = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic' -LoadBalancerBackendAddressPool $bepool -Subnet $vnet.Subnets[0]
```

Recupere as informações do Pool backend para o Balanceador de Carga para confirmar que esta Interface de Rede é adicionada ao Pool backend:
```powershell
Get-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup  -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName -BackendAddressPool  $bePool  
```

Crie uma nova Máquina Virtual e anexe a Interface de Rede para a colocar no Pool Backend:
```powershell
# Create a username and password for the virtual machine
$cred = Get-Credential

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName 'myVM1' -VMSize Standard_DS1_v2 `
 | Set-AzVMOperatingSystem -Windows -ComputerName 'myVM1' -Credential $cred `
 | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2019-Datacenter -Version latest `
 | Add-AzVMNetworkInterface -Id $nicVM1.Id
 
# Create a virtual machine using the configuration
$vm1 = New-AzVM -ResourceGroupName $rgName -Zone 1 -Location $location -VM $vmConfig
```


  
### <a name="cli"></a>CLI
Criar a Piscina backend:
```bash
az network lb address-pool create --resourceGroup myResourceGroup --lb-name myLB --name myBackendPool 
```

Crie uma nova Interface de Rede e adicione-a ao Pool Backend:
```bash
az network nic create --resource-group myResourceGroup --name myNic --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup --lb-name myLB --lb-address-pools myBackEndPool
```

Recupere o Pool backend para confirmar que o endereço IP foi corretamente adicionado:
```bash
az network lb address-pool show -g MyResourceGroup --lb-name MyLb -n MyBackendPool
```

Crie uma nova Máquina Virtual e anexe a Interface de Rede para a colocar no Pool Backend:
```bash
az vm create --resource-group myResourceGroup --name myVM --nics myNic --image UbuntuLTS --admin-username azureuser --generate-ssh-keys
```

### <a name="rest-api"></a>API REST
Criar a Piscina backend:
```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/loadBalancers/{load-balancer-name}/backendAddressPools/{backend-pool-name}?api-version=2020-05-01
```

Crie uma Interface de Rede e adicione-a ao Pool Backend que criou através da propriedade IP Configurations da Interface de Rede:

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/networkInterfaces/{nic-name}?api-version=2020-05-01
```

Corpo de Pedido JSON:
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
          "loadBalancerBackendAddressPools": {
                                    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/loadBalancers/{load-balancer-name}/backendAddressPools/{backend-pool-name}"
          }
        }
      }
    ]
  },
  "location": "eastus"
}
```

Recupere as informações do Pool backend para o Balanceador de Carga para confirmar que esta Interface de Rede é adicionada ao Pool backend:

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name/providers/Microsoft.Network/loadBalancers/{load-balancer-name/backendAddressPools/{backend-pool-name}?api-version=2020-05-01
```

Crie um VM e anexe o NIC a fazer referência ao Pool Backend:

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Compute/virtualMachines/{vm-name}?api-version=2019-12-01
```

Corpo de Pedido JSON:
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

### <a name="arm-template"></a>Modelo do ARM
Siga este [modelo DE ARM de arranque rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/101-load-balancer-standard-create/) para implementar um balanceador de carga e máquinas virtuais e adicione as máquinas virtuais ao Pool Backend via Interface de Rede.

## <a name="configuring-backend-pool-by-ip-address-and-virtual-network"></a>Configurar o Backend Pool por endereço IP e rede virtual
Se estiver a equilibrar cargas para obter recursos de contentores ou pré-povoar um Pool backend com uma gama de endereços IP, pode aproveitar o Endereço IP e a Rede Virtual para encaminhar para qualquer recurso válido, quer tenha ou não uma Interface de Rede. Ao configurar através do endereço IP e do VNET, toda a gestão do Backend Pool é feita diretamente no objeto backend Pool, conforme realçado nos exemplos abaixo.

  >[!IMPORTANT] 
  >Esta funcionalidade encontra-se atualmente em pré-visualização e tem as seguintes limitações:
  >* Limite de 100 endereços IP adicionados
  >* Os recursos de backend devem estar na mesma Rede Virtual que o Balanceador de Carga
  >* Esta funcionalidade não é atualmente suportada no Portal UX
  >* Isto só está disponível para balanceadores de carga padrão
  
### <a name="powershell"></a>PowerShell
Criar uma nova piscina de backend: 
```powershell
$backendPool = New-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup   -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPooName  
```

Atualize o pool de backend com um novo IP do VNET existente:  
```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroup 
 
$ip1 = New-AzLoadBalancerBackendAddressConfig -IpAddress "10.0.0.5" -Name "TestVNetRef" -VirtualNetwork $virtualNetwork  
 
$backendPool.LoadBalancerBackendAddresses.Add($ip1) 

Set-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup  -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName -BackendAddressPool  $backendPool  
```

Recupere as informações do Backend Pool para o Balanceador de Carga para confirmar que os endereços backend são adicionados ao Pool backend:
```powershell
Get-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup  -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName -BackendAddressPool  $backendPool  
```
Crie uma Interface de Rede e adicione-a ao Pool backend definindo o endereço IP num dos endereços backend:
```
$nic = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic' -PrivateIpAddress 10.0.0.4 -Subnet $vnet.Subnets[0]
```

Crie um VM e anexe o NIC com um endereço IP no Pool Backend:
```powershell
# Create a username and password for the virtual machine
$cred = Get-Credential

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName 'myVM' -VMSize Standard_DS1_v2 `
 | Set-AzVMOperatingSystem -Windows -ComputerName 'myVM' -Credential $cred `
 | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2019-Datacenter -Version latest `
 | Add-AzVMNetworkInterface -Id $nic.Id
 
# Create a virtual machine using the configuration
$vm = New-AzVM -ResourceGroupName $rgName -Zone 1 -Location $location -VM $vmConfig
```

### <a name="cli"></a>CLI
Utilizando o CLI, pode povoar o Pool backend através de parâmetros de linha de comando ou através de um ficheiro de configuração JSON. 

Criar e povoar o Pool backend através dos parâmetros da linha de comando:
```bash
az network lb address-pool create --lb-name myLB --name myBackendPool --vnet {VNET resource ID} --backend-address name=addr1 ip-address=10.0.0.4 --backend-address name=addr2 ip-address=10.0.0.5
```

Criar e povoar o Backend Pool através do ficheiro de configuração JSON:
```bash
az network lb address-pool create --lb-name myLB --name myBackendPool --vnet {VNET resource ID} --backend-address-config-file @config_file.json
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

Recupere as informações do Backend Pool para o Balanceador de Carga para confirmar que os endereços backend são adicionados ao Pool backend:
```bash
az network lb address-pool show -g MyResourceGroup --lb-name MyLb -n MyBackendPool
```

Crie uma Interface de Rede e adicione-a ao Pool backend definindo o endereço IP num dos endereços backend:
```bash
az network nic create \
  --resource-group myResourceGroup \
  --name myNic \
  --vnet-name myVnet \
  --subnet mySubnet \
  --network-security-group myNetworkSecurityGroup \
  --lb-name myLB \
  --private-ip-address 10.0.0.4
```

Crie um VM e anexe o NIC com um endereço IP no Pool Backend:
```bash
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --nics myNic \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

### <a name="rest-api"></a>API REST


Crie o Pool Backend e defina os endereços backend através de um pedido put backend Pool. Configure os endereços de backend que gostaria de adicionar através do Nome de Endereço, Endereço IP e ID da Rede Virtual no corpo JSON do pedido PUT:

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

Recupere as informações do Backend Pool para o Balanceador de Carga para confirmar que os endereços backend são adicionados ao Pool backend:
```
GET https://management.azure.com/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/loadBalancers/{load-balancer-name}/backendAddressPools/{backend-pool-name}?api-version=2020-05-01
```

Crie uma Interface de Rede e adicione-a ao Pool backend definindo o endereço IP num dos endereços backend:
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

Crie um VM e anexe o NIC com um endereço IP no Pool Backend:

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

### <a name="arm-template"></a>Modelo do ARM
Crie o Balancer de Carga, o Pool backend e povoe o Pool Backend com endereços backend:
```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "loadBalancers_myLB_location": {
            "type": "SecureString"
        },
        "loadBalancers_myLB_location_1": {
            "type": "SecureString"
        },
        "backendAddressPools_myBackendPool_location": {
            "type": "SecureString"
        },
        "backendAddressPools_myBackendPool_location_1": {
            "type": "SecureString"
        },
        "loadBalancers_myLB_name": {
            "defaultValue": "myLB",
            "type": "String"
        },
        "virtualNetworks_myVNET_externalid": {
            "defaultValue": "/subscriptions/6bb4a28a-db84-4e8a-b1dc-fabf7bd9f0b2/resourceGroups/ErRobin4/providers/Microsoft.Network/virtualNetworks/myVNET",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2020-04-01",
            "name": "[parameters('loadBalancers_myLB_name')]",
            "location": "eastus",
            "sku": {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "LoadBalancerFrontEnd",
                        "properties": {
                            "privateIPAddress": "10.0.0.7",
                            "privateIPAllocationMethod": "Dynamic",
                            "subnet": {
                                "id": "[concat(parameters('virtualNetworks_myVNET_externalid'), '/subnets/Subnet-1')]"
                            },
                            "privateIPAddressVersion": "IPv4"
                        }
                    }
                ],
                "backendAddressPools": [
                    {
                        "name": "myBackendPool",
                        "properties": {
                            "loadBalancerBackendAddresses": [
                                {
                                    "name": "addr1",
                                    "properties": {
                                        "ipAddress": "10.0.0.4",
                                        "virtualNetwork": {
                                            "location": "[parameters('loadBalancers_myLB_location')]"
                                        }
                                    }
                                },
                                {
                                    "name": "addr2",
                                    "properties": {
                                        "ipAddress": "10.0.0.5",
                                        "virtualNetwork": {
                                            "location": "[parameters('loadBalancers_myLB_location_1')]"
                                        }
                                    }
                                }
                            ]
                        }
                    }
                ],
                "loadBalancingRules": [],
                "probes": [],
                "inboundNatRules": [],
                "outboundRules": [],
                "inboundNatPools": []
            }
        },
        {
            "type": "Microsoft.Network/loadBalancers/backendAddressPools",
            "apiVersion": "2020-04-01",
            "name": "[concat(parameters('loadBalancers_myLB_name'), '/myBackendPool')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLB_name'))]"
            ],
            "properties": {
                "loadBalancerBackendAddresses": [
                    {
                        "name": "addr1",
                        "properties": {
                            "ipAddress": "10.0.0.4",
                            "virtualNetwork": {
                                "location": "[parameters('backendAddressPools_myBackendPool_location')]"
                            }
                        }
                    },
                    {
                        "name": "addr2",
                        "properties": {
                            "ipAddress": "10.0.0.5",
                            "virtualNetwork": {
                                "location": "[parameters('backendAddressPools_myBackendPool_location_1')]"
                            }
                        }
                    }
                ]
            }
        }
    ]
}
```

Crie uma Máquina Virtual e uma Interface de Rede anexada. Desaponuse o endereço IP da Interface de Rede para um dos endereços backend:
```
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "String",
      "metadata": {
        "description": "Name of storage account"
      }
    },
    "storageAccountDomain": {
      "type": "String",
      "metadata": {
        "description": "The domain of the storage account to be created."
      }
    },
    "adminUsername": {
      "type": "String",
      "metadata": {
        "description": "Admin username"
      }
    },
    "adminPassword": {
      "type": "SecureString",
      "metadata": {
        "description": "Admin password"
      }
    },
    "vmName": {
      "defaultValue": "myVM",
      "type": "String",
      "metadata": {
        "description": "Prefix to use for VM names"
      }
    },
    "imagePublisher": {
      "type": "String",
      "metadata": {
        "description": "Image Publisher"
      }
    },
    "imageOffer": {
      "defaultValue": "WindowsServer",
      "type": "String",
      "metadata": {
        "description": "Image Offer"
      }
    },
    "imageSKU": {
      "defaultValue": "2012-R2-Datacenter",
      "type": "String",
      "metadata": {
        "description": "Image SKU"
      }
    },
    "lbName": {
      "defaultValue": "myLB",
      "type": "String",
      "metadata": {
        "description": "Load Balancer name"
      }
    },
    "nicName": {
      "defaultValue": "nic",
      "type": "String",
      "metadata": {
        "description": "Network Interface name prefix"
      }
    },
    "privateIpAddress": {
      "defaultValue": "10.0.0.5",
      "type": "String",
      "metadata": {
        "description": "Private IP Address of the VM"
      }
    },
    "vnetName": {
      "defaultValue": "myVNET",
      "type": "String",
      "metadata": {
        "description": "VNET name"
      }
    },
    "vmSize": {
      "defaultValue": "Standard_A1",
      "type": "String",
      "metadata": {
        "description": "Size of the VM"
      }
    },
    "storageLocation": {
      "type": "String",
      "metadata": {
        "description": "Location of the Storage Account."
      }
    },
    "location": {
      "type": "String",
      "metadata": {
        "description": "Location to deploy all the resources in."
      }
    }
  },
  "variables": {
    "networkSecurityGroupName": "networkSecurityGroup1",
    "storageAccountType": "Standard_LRS",
    "subnetName": "Subnet-1",
    "publicIPAddressType": "Static",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('storageLocation')]",
      "properties": {
        "accountType": "[variables('storageAccountType')]"
      }
    },
    {
      "type": "Microsoft.Network/networkSecurityGroups",
      "apiVersion": "2016-03-30",
      "name": "[variables('networkSecurityGroupName')]",
      "location": "[parameters('location')]",
      "properties": {
        "securityRules": []
      }
    },
    {
      "type": "Microsoft.Network/networkInterfaces",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Static",
              "privateIpAddress": "[parameters('privateIpAddress')]",
              "subnet": {
                "id": "[variables('subnetRef')]"
              }
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('vmName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
        "[parameters('nicName')]"
      ],
      "properties": {
        "hardwareProfile": {
          "vmSize": "[parameters('vmSize')]"
        },
        "osProfile": {
          "computername": "[parameters('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "[parameters('imagePublisher')]",
            "offer": "[parameters('imageOffer')]",
            "sku": "[parameters('imageSKU')]",
            "version": "latest"
          },
          "osDisk": {
            "name": "osdisk",
            "vhd": {
              "uri": "[concat('http://',parameters('storageAccountName'),'.blob.',parameters('storageAccountDomain'),'/vhds/','osdisk', '.vhd')]"
            },
            "caching": "ReadWrite",
            "createOption": "FromImage"
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
            }
          ]
        }
      }
    }
  ]
}
```
