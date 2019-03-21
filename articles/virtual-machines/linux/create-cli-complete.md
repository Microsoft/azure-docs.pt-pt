---
title: Criar um ambiente do Linux com a CLI do Azure | Documentos da Microsoft
description: Crie armazenamento, uma VM do Linux, uma rede virtual e sub-rede, um balanceador de carga, uma NIC, um IP público e um grupo de segurança de rede, tudo a partir do zero com a CLI do Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2017
ms.author: cynthn
ms.openlocfilehash: eb4c5897cdadecd074c2764faceeed13f4c724c3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58008731"
---
# <a name="create-a-complete-linux-virtual-machine-with-the-azure-cli"></a>Criar uma máquina de virtual de Linux completa com a CLI do Azure
Para criar rapidamente uma máquina virtual (VM) no Azure, pode utilizar um único comando da CLI do Azure que utiliza valores predefinidos para criar quaisquer recursos de suporte necessários. Recursos como uma rede virtual, endereço IP público e regras de grupo de segurança de rede são criados automaticamente. Para obter mais controlo do ambiente de produção utilizar, pode criar estes recursos antecipadamente e, em seguida, adicione as suas VMs a eles. Este artigo orienta-o através de como criar uma VM e cada um dos recursos de suporte individualmente.

Certifique-se de que instalou a versão mais recente [CLI do Azure](/cli/azure/install-az-cli2) e uma conta do Azure com a sessão iniciada [início de sessão az](/cli/azure/reference-index).

Nos exemplos a seguir, substitua os nomes de parâmetros de exemplo pelos seus próprios valores. Os nomes de parâmetros de exemplo incluem *myResourceGroup*, *myVnet*, e *myVM*.

## <a name="create-resource-group"></a>Criar grupo de recursos
Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Um grupo de recursos tem de ser criado antes de uma máquina virtual e recursos de rede virtual. Criar o grupo de recursos com [criar grupo az](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Por predefinição, a saída dos comandos da CLI do Azure é em JSON (JavaScript Object Notation). Para alterar a predefinição de saída a uma lista ou tabela, por exemplo, utilize [configurar a az - saída](/cli/azure/reference-index). Também pode adicionar `--output` para qualquer comando por um tempo de alterar no formato de saída. O exemplo seguinte mostra a saída JSON do `az group create` comando:

```json                       
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "location": "eastus",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-a-virtual-network-and-subnet"></a>Criar uma rede virtual e uma sub-rede
Seguinte que cria uma rede virtual no Azure e uma sub-rede para o qual pode criar as suas VMs. Uso [criar a vnet de rede de az](/cli/azure/network/vnet) para criar uma rede virtual denominada *myVnet* com o *192.168.0.0/16* prefixo de endereço. Também adicionar uma sub-rede denominada *mySubnet* com o prefixo de endereço *192.168.1.0/24*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

O resultado mostra que a sub-rede é logicamente criada dentro da rede virtual:

```json
{
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "location": "eastus",
  "name": "myVnet",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "ed62fd03-e9de-430b-84df-8a3b87cacdbb",
  "subnets": [
    {
      "addressPrefix": "192.168.1.0/24",
      "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
      "ipConfigurations": null,
      "name": "mySubnet",
      "networkSecurityGroup": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "resourceNavigationLinks": null,
      "routeTable": null
    }
  ],
  "tags": {},
  "type": "Microsoft.Network/virtualNetworks",
  "virtualNetworkPeerings": null
}
```


## <a name="create-a-public-ip-address"></a>Crie um endereço IP público
Agora vamos criar um endereço IP público [criar a rede de az public-ip](/cli/azure/network/public-ip). Este endereço IP público permite-lhe ligar às suas VMs a partir da Internet. Como o endereço predefinido é dinâmico, criar uma entrada DNS com o nome com o `--domain-name-label` parâmetro. O exemplo seguinte cria um IP público com o nome *myPublicIP* com o nome DNS *mypublicdns*. Uma vez que o nome DNS tem de ser exclusivo, forneça o nome DNS exclusivo:

```azurecli
az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --dns-name mypublicdns
```

Saída:

```json
{
  "publicIp": {
    "dnsSettings": {
      "domainNameLabel": "mypublicdns",
      "fqdn": "mypublicdns.eastus.cloudapp.azure.com",
      "reverseFqdn": null
    },
    "etag": "W/\"2632aa72-3d2d-4529-b38e-b622b4202925\"",
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": null,
    "ipConfiguration": null,
    "location": "eastus",
    "name": "myPublicIP",
    "provisioningState": "Succeeded",
    "publicIpAddressVersion": "IPv4",
    "publicIpAllocationMethod": "Dynamic",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "4c65de38-71f5-4684-be10-75e605b3e41f",
    "tags": null,
    "type": "Microsoft.Network/publicIPAddresses"
  }
}
```


## <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede
Para controlar o fluxo de tráfego que entra e sai as suas VMs, é possível aplicar um grupo de segurança de rede a um NIC ou sub-rede virtual. O exemplo seguinte utiliza [criar o nsg de rede de az](/cli/azure/network/nsg) criar um grupo de segurança de rede com o nome *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Pode definir regras que permitem ou negam o tráfego específico. Para permitir ligações de entrada na porta 22 (para habilitar o acesso SSH), crie uma regra de entrada com [criar regra de nsg de rede de az](/cli/azure/network/nsg/rule). O exemplo seguinte cria uma regra com o nome *myNetworkSecurityGroupRuleSSH*:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleSSH \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 22 \
    --access allow
```

Para permitir ligações de entrada na porta 80 (para o tráfego da web), adicione outra regra de grupo de segurança de rede. O exemplo seguinte cria uma regra com o nome *myNetworkSecurityGroupRuleHTTP*:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleWeb \
    --protocol tcp \
    --priority 1001 \
    --destination-port-range 80 \
    --access allow
```

Examine o grupo de segurança de rede e regras com [show de nsg de rede de az](/cli/azure/network/nsg):

```azurecli
az network nsg show --resource-group myResourceGroup --name myNetworkSecurityGroup
```

Saída:

```json
{
  "defaultSecurityRules": [
    {
      "access": "Allow",
      "description": "Allow inbound traffic from all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetInBound",
      "name": "AllowVnetInBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow inbound traffic from azure load balancer",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowAzureLoadBalancerInBou",
      "name": "AllowAzureLoadBalancerInBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "AzureLoadBalancer",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all inbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllInBound",
      "name": "DenyAllInBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetOutBound",
      "name": "AllowVnetOutBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to Internet",
      "destinationAddressPrefix": "Internet",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowInternetOutBound",
      "name": "AllowInternetOutBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all outbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllOutBound",
      "name": "DenyAllOutBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
  "location": "eastus",
  "name": "myNetworkSecurityGroup",
  "networkInterfaces": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "47a9964e-23a3-438a-a726-8d60ebbb1c3c",
  "securityRules": [
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "22",
      "direction": "Inbound",
      "etag": "W/\"9e344b60-0daa-40a6-84f9-0ebbe4a4b640\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleSSH",
      "name": "myNetworkSecurityGroupRuleSSH",
      "priority": 1000,
      "protocol": "Tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "80",
      "direction": "Inbound",
      "etag": "W/\"9e344b60-0daa-40a6-84f9-0ebbe4a4b640\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleWeb",
      "name": "myNetworkSecurityGroupRuleWeb",
      "priority": 1001,
      "protocol": "Tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "subnets": null,
  "tags": null,
  "type": "Microsoft.Network/networkSecurityGroups"
}
```

## <a name="create-a-virtual-nic"></a>Crie um NIC virtual
Placas de interface de rede virtual (NICs) estão disponíveis por meio de programação porque pode aplicar regras de seu uso. Consoante a [tamanho da VM](sizes.md), pode anexar vários NICs virtuais a uma VM. A seguir [criar az rede nic](/cli/azure/network/nic) comando, crie um NIC com o nome *myNic* e associá-la com o seu grupo de segurança de rede. O endereço IP público *myPublicIP* está também associado a NIC virtual.

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIP \
    --network-security-group myNetworkSecurityGroup
```

Saída:

```json
{
  "NewNIC": {
    "dnsSettings": {
      "appliedDnsServers": [],
      "dnsServers": [],
      "internalDnsNameLabel": null,
      "internalDomainNameSuffix": "brqlt10lvoxedgkeuomc4pm5tb.bx.internal.cloudapp.net",
      "internalFqdn": null
    },
    "enableAcceleratedNetworking": false,
    "enableIpForwarding": false,
    "etag": "W/\"04b5ab44-d8f4-422a-9541-e5ae7de8466d\"",
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
    "ipConfigurations": [
      {
        "applicationGatewayBackendAddressPools": null,
        "etag": "W/\"04b5ab44-d8f4-422a-9541-e5ae7de8466d\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic/ipConfigurations/ipconfig1",
        "loadBalancerBackendAddressPools": null,
        "loadBalancerInboundNatRules": null,
        "name": "ipconfig1",
        "primary": true,
        "privateIpAddress": "192.168.1.4",
        "privateIpAddressVersion": "IPv4",
        "privateIpAllocationMethod": "Dynamic",
        "provisioningState": "Succeeded",
        "publicIpAddress": {
          "dnsSettings": null,
          "etag": null,
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
          "idleTimeoutInMinutes": null,
          "ipAddress": null,
          "ipConfiguration": null,
          "location": null,
          "name": null,
          "provisioningState": null,
          "publicIpAddressVersion": null,
          "publicIpAllocationMethod": null,
          "resourceGroup": "myResourceGroup",
          "resourceGuid": null,
          "tags": null,
          "type": null
        },
        "resourceGroup": "myResourceGroup",
        "subnet": {
          "addressPrefix": null,
          "etag": null,
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
          "ipConfigurations": null,
          "name": null,
          "networkSecurityGroup": null,
          "provisioningState": null,
          "resourceGroup": "myResourceGroup",
          "resourceNavigationLinks": null,
          "routeTable": null
        }
      }
    ],
    "location": "eastus",
    "macAddress": null,
    "name": "myNic",
    "networkSecurityGroup": {
      "defaultSecurityRules": null,
      "etag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
      "location": null,
      "name": null,
      "networkInterfaces": null,
      "provisioningState": null,
      "resourceGroup": "myResourceGroup",
      "resourceGuid": null,
      "securityRules": null,
      "subnets": null,
      "tags": null,
      "type": null
    },
    "primary": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "b3dbaa0e-2cf2-43be-a814-5cc49fea3304",
    "tags": null,
    "type": "Microsoft.Network/networkInterfaces",
    "virtualMachine": null
  }
}
```


## <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade
Conjuntos de disponibilidade ajuda spread as suas VMs em domínios de falha e domínios de atualização. Mesmo que apenas criar uma VM neste momento, é melhor prática para utilizar conjuntos de disponibilidade para facilitar a expandir no futuro. 

Domínios de falha definem um agrupamento de máquinas virtuais que partilham um comutador de rede e fonte de energia comum. Por predefinição, as máquinas virtuais que estejam configuradas no seu conjunto de disponibilidade estão separadas por até três domínios de falha. Um problema de hardware em um destes domínios de falhas não afeta todas as VMS que está a executar a aplicação.

Domínios de atualização indicam os grupos de máquinas virtuais e o hardware físico subjacente que pode ser reiniciado ao mesmo tempo. Durante a manutenção planeada, a ordem na qual atualização domínios são reiniciados não pode ser sequencial, mas apenas um domínio de atualização é reiniciado de cada vez.

Azure distribui automaticamente as VMs entre os domínios de falha e de atualização quando colocá-las num conjunto de disponibilidade. Para obter mais informações, consulte [o gerenciamento de VMs](manage-availability.md).

Criar conjunto de disponibilidade para a VM com [criar conjunto de disponibilidade do az vm](/cli/azure/vm/availability-set). O exemplo seguinte cria um conjunto de disponibilidade designado *myAvailabilitySet*:

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet
```

As notas de saída domínios de falha e domínios de atualização:

```json
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet",
  "location": "eastus",
  "managed": null,
  "name": "myAvailabilitySet",
  "platformFaultDomainCount": 2,
  "platformUpdateDomainCount": 5,
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": null,
    "managed": true,
    "tier": null
  },
  "statuses": null,
  "tags": {},
  "type": "Microsoft.Compute/availabilitySets",
  "virtualMachines": []
}
```


## <a name="create-a-vm"></a>Criar uma VM
Acabou de criar os recursos de rede para suportar VMs acessível pela Internet. Agora, crie uma VM e protegê-la com uma chave SSH. Neste exemplo, vamos criar um VM com base no mais recente LTS do Ubuntu. Pode encontrar imagens adicionais com [lista de imagens de vm de az](/cli/azure/vm/image), conforme descrito nas [encontrar imagens de VM do Azure](cli-ps-findimage.md).

Especifique uma chave SSH para utilizar para autenticação. Se não tiver um par de chaves públicas SSH, pode [criá-los](mac-create-ssh-keys.md) ou utilizar o `--generate-ssh-keys` parâmetro criá-los para. Se já tiver um par de chaves, este parâmetro utiliza as chaves existentes no `~/.ssh`.

Criar a VM ao trazer todos os recursos e informações em conjunto com o [az vm criar](/cli/azure/vm) comando. O exemplo seguinte cria uma VM com o nome *myVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --availability-set myAvailabilitySet \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH para a VM com a entrada DNS que forneceu quando criou o endereço IP público. Isso `fqdn` é apresentada no resultado, como criar a sua VM:

```json
{
  "fqdns": "mypublicdns.eastus.cloudapp.azure.com",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-13-71-C8",
  "powerState": "VM running",
  "privateIpAddress": "192.168.1.5",
  "publicIpAddress": "13.90.94.252",
  "resourceGroup": "myResourceGroup"
}
```

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Saída:

```bash
The authenticity of host 'mypublicdns.eastus.cloudapp.azure.com (13.90.94.252)' can't be established.
ECDSA key fingerprint is SHA256:SylINP80Um6XRTvWiFaNz+H+1jcrKB1IiNgCDDJRj6A.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'mypublicdns.eastus.cloudapp.azure.com,13.90.94.252' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.11.0-1016-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    https://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@myVM:~$
```

Pode instalar o NGINX e ver o tráfego de fluxo para a VM. Instale o NGINX da seguinte forma:

```bash
sudo apt-get install -y nginx
```

Para ver site NGINX predefinido em ação, abra o browser e introduza o FQDN:

![Site predefinido do NGINX na sua VM](media/create-cli-complete/nginx.png)

## <a name="export-as-a-template"></a>Exportar como modelo
E se pretende agora criar um ambiente de desenvolvimento adicional com os mesmos parâmetros, ou a um ambiente de produção que corresponda ao-lo? Gestor de recursos usa os modelos JSON que definem a todos os parâmetros para o seu ambiente. Crie os ambientes completos fazendo referência este modelo JSON. Pode [criar manualmente os modelos JSON](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou exportar um ambiente existente para criar o modelo JSON para. Uso [exportação de grupo az](/cli/azure/group) para exportar o seu grupo de recursos da seguinte forma:

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

Este comando cria o `myResourceGroup.json` ficheiro no diretório de trabalho atual. Quando criar um ambiente a partir deste modelo, são-lhe pedido para todos os nomes de recursos. Pode preencher esses nomes em seu arquivo de modelo ao adicionar o `--include-parameter-default-value` parâmetro para o `az group export` comando. Editar o modelo JSON para especificar os nomes de recursos, ou [crie um ficheiro Parameters. JSON](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) que especifica os nomes de recursos.

Para criar um ambiente a partir do seu modelo, utilize [criar a implementação do grupo az](/cli/azure/group/deployment) da seguinte forma:

```azurecli
az group deployment create \
    --resource-group myNewResourceGroup \
    --template-file myResourceGroup.json
```

Pode querer ler [mais sobre como implementar a partir de modelos](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Saiba mais sobre como atualizar ambientes incrementalmente, utilize o ficheiro de parâmetros e modelos de acesso a partir de uma localização de armazenamento única.

## <a name="next-steps"></a>Passos Seguintes
Agora, está pronto para começar a trabalhar com vários componentes de rede e as VMs. Pode utilizar este ambiente de exemplo para criar seu aplicativo usando os componentes nucleares apresentados aqui.
