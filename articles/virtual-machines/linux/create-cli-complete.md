---
title: Criar um ambiente Linux com o Azure CLI
description: Criar armazenamento, um Linux VM, uma rede virtual e sub-rede, um equilibrador de carga, um NIC, um IP público, e um grupo de segurança de rede, tudo desde o zero através do Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 12/14/2017
ms.author: cynthn
ms.openlocfilehash: ff86651d56abe090ca08c508a220362f9a011a3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102554705"
---
# <a name="create-a-complete-linux-virtual-machine-with-the-azure-cli"></a>Crie uma máquina virtual Linux completa com o Azure CLI
Para criar rapidamente uma máquina virtual (VM) em Azure, pode utilizar um único comando Azure CLI que utiliza valores predefinidos para criar os recursos de suporte necessários. Recursos como uma rede virtual, endereço IP público e regras do grupo de segurança de rede são automaticamente criados. Para um maior controlo do seu ambiente em uso de produção, pode criar estes recursos com antecedência e, em seguida, adicionar-lhes os seus VMs. Este artigo guia-o através da forma de criar um VM e cada um dos recursos de apoio, um a um.

Certifique-se de que instalou o mais recente [Azure CLI](/cli/azure/install-az-cli2) e iniciou sessão numa conta Azure com [login az](/cli/azure/reference-index).

Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo pelos seus próprios valores. Os nomes dos parâmetros incluem *myResourceGroup,* *myVnet* e *myVM*.

## <a name="create-resource-group"></a>Criar grupo de recursos
Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Um grupo de recursos deve ser criado antes de uma máquina virtual e de suporte a recursos de rede virtuais. Criar o grupo de recursos com [a criação do grupo AZ](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *este:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Por predefinição, a saída dos comandos Azure CLI encontra-se em JSON (Notação de Objetos JavaScript). Para alterar a saída padrão para uma lista ou tabela, por exemplo, use [az configuração --saída](/cli/azure/reference-index). Também pode adicionar `--output` a qualquer comando para uma alteração única no formato de saída. O exemplo a seguir mostra a saída JSON do `az group create` comando:

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
Em seguida, cria-se uma rede virtual em Azure e uma sub-rede na qual pode criar os seus VMs. Utilize [a rede az vnet criar](/cli/azure/network/vnet) para criar uma rede virtual chamada *myVnet* com o prefixo de endereço *192.168.0.0/16.* Também adicione uma sub-rede chamada *mySubnet* com o prefixo de endereço de *192.168.1.0/24*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

A saída mostra que a sub-rede é logicamente criada dentro da rede virtual:

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
Agora vamos criar um endereço IP público com [a criação de ip público da rede Az.](/cli/azure/network/public-ip) Este endereço IP público permite-lhe ligar-se aos seus VMs a partir da Internet. Como o endereço predefinido é dinâmico, crie uma entrada DE DNS com o `--domain-name-label` parâmetro. O exemplo a seguir cria um IP público chamado *myPublicIP* com o nome DNS de *mypublicdns*. Como o nome DNS deve ser único, forneça o seu próprio nome DNS único:

```azurecli
az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --dns-name mypublicdns
```

Resultado:

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
Para controlar o fluxo de tráfego dentro e fora dos seus VMs, aplica um grupo de segurança de rede a um NIC ou sub-rede virtual. O exemplo a seguir utiliza [a criação de nsg de rede az](/cli/azure/network/nsg) para criar um grupo de segurança de rede chamado *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Define regras que permitem ou negam tráfego específico. Para permitir ligações de entrada na porta 22 (para permitir o acesso ao SSH), crie uma regra de entrada com [a regra de rede az nsg criar](/cli/azure/network/nsg/rule). O exemplo a seguir cria uma regra chamada *myNetworkSecurityGroupRuleSSH:*

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

Para permitir ligações de entrada na porta 80 (para tráfego web), adicione outra regra do grupo de segurança da rede. O exemplo a seguir cria uma regra chamada *myNetworkSecurityGroupRuleHTTP:*

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

Examine o grupo de segurança da rede e as regras com [a rede az nsg show:](/cli/azure/network/nsg)

```azurecli
az network nsg show --resource-group myResourceGroup --name myNetworkSecurityGroup
```

Resultado:

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

## <a name="create-a-virtual-nic"></a>Criar um NIC virtual
Os cartões de interface de rede virtual (NICs) estão disponíveis programáticamente porque pode aplicar regras à sua utilização. Dependendo do [tamanho de VM,](../sizes.md)pode anexar vários NICs virtuais a um VM. No seguinte [esítrupo de rede az,](/cli/azure/network/nic) cria-se um NIC chamado *myNic* e associá-lo ao seu grupo de segurança de rede. O endereço IP público *myPublicIP* também está associado ao NIC virtual.

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIP \
    --network-security-group myNetworkSecurityGroup
```

Resultado:

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
Os conjuntos de disponibilidade ajudam a espalhar os seus VMs por domínios de falhas e domínios de atualização. Apesar de só criar um VM neste momento, é melhor utilizar conjuntos de disponibilidade para facilitar a expansão no futuro. 

Os domínios de avaria definem um agrupamento de máquinas virtuais que partilham uma fonte de energia comum e um interruptor de rede. Por predefinição, as máquinas virtuais configuradas dentro do seu conjunto de disponibilidade são separadas em três domínios de avaria. Um problema de hardware num destes domínios de avaria não afeta todos os VM que estão a executar a sua aplicação.

Os domínios de atualização indicam grupos de máquinas virtuais e hardware físico subjacente que podem ser reiniciados ao mesmo tempo. Durante a manutenção planeada, a ordem em que os domínios de atualização são reiniciados pode não ser sequencial, mas apenas um domínio de atualização é reiniciado de cada vez.

O Azure distribui automaticamente VMs pelos domínios de falha e atualização ao colocá-los num conjunto de disponibilidade. Para mais informações, consulte [a gestão da disponibilidade de VMs.](../availability.md)

Crie um conjunto de disponibilidade para o seu VM com [a criação de disponibilidade de vm az.](/cli/azure/vm/availability-set) O exemplo seguinte cria um conjunto de disponibilidade com o nome *myAvailabilitySet*:

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet
```

A saída regista domínios de avaria e domínios de atualização:

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
Criou os recursos de rede para apoiar VMs acessíveis à Internet. Agora crie um VM e prenda-o com uma chave SSH. Neste exemplo, vamos criar um VM Ubuntu baseado no mais recente LTS. Pode encontrar imagens adicionais com [a lista de imagens AZ VM](/cli/azure/vm/image), como descrito ao [encontrar imagens Azure VM](cli-ps-findimage.md).

Especifique uma chave SSH para a autenticação. Se não tiver um par de chaves públicas SSH, pode [criá-los](mac-create-ssh-keys.md) ou usar o `--generate-ssh-keys` parâmetro para criá-los para si. Se já tem um par de chaves, este parâmetro utiliza as teclas existentes em `~/.ssh` .

Crie o VM juntando todos os recursos e informações com o comando [az vm create.](/cli/azure/vm) O exemplo a seguir cria um VM chamado *myVM:*

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

SSH para o seu VM com a entrada DNS que forneceu quando criou o endereço IP público. Isto `fqdn` é mostrado na saída à medida que cria o seu VM:

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

Resultado:

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

Pode instalar o NGINX e ver o fluxo de tráfego para o VM. Instale o NGINX da seguinte forma:

```bash
sudo apt-get install -y nginx
```

Para ver o site NGINX padrão em ação, abra o seu navegador web e insira o seu FQDN:

![Site NGINX predefinido no seu VM](media/create-cli-complete/nginx.png)

## <a name="export-as-a-template"></a>Exportação como modelo
E se agora quiser criar um ambiente de desenvolvimento adicional com os mesmos parâmetros, ou um ambiente de produção que o corresponda? O Gestor de Recursos utiliza modelos JSON que definem todos os parâmetros para o seu ambiente. Você constrói ambientes inteiros fazendo referência a este modelo JSON. Pode [construir modelos JSON manualmente](../../azure-resource-manager/templates/template-syntax.md?toc=/azure/virtual-machines/linux/toc.json) ou exportar um ambiente existente para criar o modelo JSON para si. Utilize [a exportação do grupo AZ](/cli/azure/group) para exportar o seu grupo de recursos da seguinte forma:

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

Este comando cria o `myResourceGroup.json` ficheiro no seu diretório de trabalho atual. Quando cria um ambiente a partir deste modelo, é solicitado para todos os nomes de recursos. Pode preencher estes nomes no seu ficheiro de modelo adicionando o `--include-parameter-default-value` parâmetro ao `az group export` comando. Edite o seu modelo JSON para especificar os nomes dos recursos ou [crie uma parameters.jsno ficheiro](../../azure-resource-manager/templates/template-syntax.md?toc=/azure/virtual-machines/linux/toc.json) que especifique os nomes dos recursos.

Para criar um ambiente a partir do seu modelo, use [o grupo de implementação az criar](/cli/azure/deployment/group) da seguinte forma:

```azurecli
az deployment group create \
    --resource-group myNewResourceGroup \
    --template-file myResourceGroup.json
```

Você pode querer ler [mais sobre como implementar a partir de modelos](../../azure-resource-manager/templates/deploy-cli.md?toc=/azure/virtual-machines/linux/toc.json). Saiba como atualizar gradualmente os ambientes, usar o ficheiro de parâmetros e aceder a modelos a partir de um único local de armazenamento.

## <a name="next-steps"></a>Passos seguintes
Agora está pronto para começar a trabalhar com vários componentes de rede e VMs. Pode utilizar este ambiente de amostra para construir a sua aplicação utilizando os componentes centrais introduzidos aqui.