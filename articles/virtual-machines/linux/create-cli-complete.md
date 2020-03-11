---
title: Crie um ambiente Linux com o Azure CLI
description: Crie armazenamento, um Linux VM, uma rede virtual e uma subnet, um equilibrador de carga, um NIC, um IP público, e um grupo de segurança de rede, tudo de zero através do Azure CLI.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 12/14/2017
ms.author: cynthn
ms.openlocfilehash: 7ee4674f5e7c04709256459c3417a1379a65aedc
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969550"
---
# <a name="create-a-complete-linux-virtual-machine-with-the-azure-cli"></a>Crie uma máquina virtual Linux completa com o Azure CLI
Para criar rapidamente uma máquina virtual (VM) em Azure, pode utilizar um único comando Azure CLI que utiliza valores padrão para criar os recursos de apoio necessários. São automaticamente criados recursos como uma rede virtual, endereço IP público e regras do grupo de segurança da rede. Para um maior controlo do seu ambiente no uso da produção, poderá criar estes recursos com antecedência e, em seguida, adicionar-lhes os seus VMs. Este artigo guia-o através de como criar um VM e cada um dos recursos de apoio, um a um.

Certifique-se de que instalou o mais recente [Azure CLI](/cli/azure/install-az-cli2) e iniciou uma conta Azure com [login az](/cli/azure/reference-index).

Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo pelos seus próprios valores. Exemplo nomes de parâmetros incluem *myResourceGroup,* *myVnet,* e *myVM*.

## <a name="create-resource-group"></a>Criar grupo de recursos
Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Um grupo de recursos deve ser criado antes de uma máquina virtual e apoiar os recursos de rede virtuais. Crie o grupo de recursos com o [grupo Az criar](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Por predefinição, a saída dos comandos Azure CLI está em JSON (Notação de Objetos JavaScript). Para alterar a saída predefinida para uma lista ou tabela, por exemplo, utilize [az configurar --output](/cli/azure/reference-index). Também pode adicionar `--output` a qualquer comando para uma alteração de uma vez no formato de saída. O exemplo seguinte mostra a saída JSON do comando `az group create`:

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
Em seguida, cria uma rede virtual em Azure e uma sub-rede na qual pode criar os seus VMs. Use a [rede az vnet criar](/cli/azure/network/vnet) para criar uma rede virtual chamada *myVnet* com o prefixo de endereço *192.168.0.0.0/16.* Adicione também uma sub-rede denominada *mySubnet* com o prefixo de endereço de *192.168.1.0/24:*

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

A saída mostra que a subneta é logicamente criada dentro da rede virtual:

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
Agora vamos criar um endereço IP público com a [rede az public-ip criar](/cli/azure/network/public-ip). Este endereço IP público permite-lhe ligar-se aos seus VMs a partir da Internet. Como o endereço predefinido é dinâmico, crie uma entrada DNS com o parâmetro `--domain-name-label`. O exemplo seguinte cria um IP público chamado *myPublicIP* com o nome DNS dos *meus publicdns*. Como o nome DNS deve ser único, forneça o seu próprio nome DNS único:

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
Para controlar o fluxo de tráfego dentro e fora dos seus VMs, aplica um grupo de segurança de rede a um NIC ou subnet virtual. O exemplo seguinte usa a [rede Az nsg criar](/cli/azure/network/nsg) para criar um grupo de segurança de rede chamado *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Define regras que permitem ou negam tráfego específico. Para permitir ligações de entrada na porta 22 (para permitir o acesso ao SSH), crie uma regra de entrada com a regra nsg da [rede Az criar](/cli/azure/network/nsg/rule). O exemplo seguinte cria uma regra chamada *myNetworkSecurityGroupRuleSSH:*

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

Para permitir ligações de entrada na porta 80 (para tráfego web), adicione outra regra do grupo de segurança da rede. O exemplo seguinte cria uma regra chamada *myNetworkSecurityGroupRuleHTTP:*

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

Examine o grupo de segurança da rede e as regras com a [rede AZ NSG show](/cli/azure/network/nsg):

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

## <a name="create-a-virtual-nic"></a>Criar um NIC virtual
Os cartões de interface de rede virtuais (NICs) estão disponíveis programáticamente porque pode aplicar regras à sua utilização. Dependendo do [tamanho vM,](sizes.md)pode anexar vários NICs virtuais a um VM. No seguinte comando de [criação de rede az](/cli/azure/network/nic) nic, você cria um NIC chamado *myNic* e associa-o ao seu grupo de segurança de rede. O endereço IP público *myPublicIP* também está associado ao NIC virtual.

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
Os conjuntos de disponibilidade ajudam a espalhar os seus VMs através de domínios de falhas e a atualizar domínios. Mesmo que você só crie um VM agora, é melhor usar conjuntos de disponibilidade para facilitar a expansão no futuro. 

Os domínios de falha definem um agrupamento de máquinas virtuais que partilham uma fonte de energia comum e um interruptor de rede. Por predefinição, as máquinas virtuais que estão configuradas dentro do seu conjunto de disponibilidade estão separadas em até três domínios de falha. Um problema de hardware num destes domínios de falha não afeta todos os VM que estão a executar a sua aplicação.

Os domínios de atualização indicam grupos de máquinas virtuais e hardware físico subjacente que podem ser reiniciados ao mesmo tempo. Durante a manutenção planeada, a ordem em que os domínios de atualização são reiniciados pode não ser sequencial, mas apenas um domínio de atualização é reiniciado de cada vez.

O Azure distribui automaticamente VMs através da falha e atualiza os domínios ao colocá-los num conjunto de disponibilidade. Para mais informações, consulte [a gestão da disponibilidade de VMs](manage-availability.md).

Crie um conjunto de disponibilidade para o seu VM com [az vm disponibilidade-set criar](/cli/azure/vm/availability-set). O exemplo seguinte cria um conjunto de disponibilidade designado *myAvailabilitySet*:

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet
```

A saída nota domínios de falha e atualização de domínios:

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
Criou os recursos da rede para apoiar vMs acessíveis à Internet. Agora crie um VM e prenda-o com uma chave SSH. Neste exemplo, vamos criar um Ubuntu VM baseado no MAIS recente LTS. Pode encontrar imagens adicionais com a lista de [imagens az vm,](/cli/azure/vm/image)como descrito na descoberta de [imagens Azure VM](cli-ps-findimage.md).

Especifique uma tecla SSH para a autenticação. Se não tiver um par de chaves públicas SSH, pode [criá-los](mac-create-ssh-keys.md) ou utilizar o parâmetro `--generate-ssh-keys` para criá-los para si. Se já tiver um par de chaves, este parâmetro utiliza as chaves existentes em `~/.ssh`.

Crie o VM reunindo todos os recursos e informações com o comando [az vm criar.](/cli/azure/vm) O exemplo seguinte cria uma VM com o nome *myVM*:

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

SSH para o seu VM com a entrada DNS que forneceu quando criou o endereço IP público. Esta `fqdn` é mostrada na saída à medida que cria o seu VM:

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

Pode instalar o NGINX e ver o fluxo de tráfego para o VM. Instale o NGINX da seguinte forma:

```bash
sudo apt-get install -y nginx
```

Para ver o site padrão NGINX em ação, abra o seu navegador web e introduza o fQDN:

![Site padrão NGINX no seu VM](media/create-cli-complete/nginx.png)

## <a name="export-as-a-template"></a>Exportar como modelo
E se quiser agora criar um ambiente de desenvolvimento adicional com os mesmos parâmetros, ou um ambiente de produção que o corresponda? O Gestor de Recursos utiliza modelos JSON que definem todos os parâmetros para o seu ambiente. Você constrói ambientes inteiros fazendo referência a este modelo JSON. Você pode [construir modelos JSON manualmente](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou exportar um ambiente existente para criar o modelo JSON para você. Utilize a exportação do [grupo AZ](/cli/azure/group) para exportar o seu grupo de recursos da seguinte forma:

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

Este comando cria o ficheiro `myResourceGroup.json` no seu atual diretório de trabalho. Quando você cria um ambiente a partir deste modelo, você é solicitado para todos os nomes de recursos. Pode povoar estes nomes no seu ficheiro de modelo adicionando o parâmetro `--include-parameter-default-value` ao comando `az group export`. Edite o seu modelo JSON para especificar os nomes dos recursos ou [crie um ficheiro parameters.json](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) que especifica os nomes dos recursos.

Para criar um ambiente a partir do seu modelo, use a implementação do [grupo Az criar](/cli/azure/group/deployment) da seguinte forma:

```azurecli
az group deployment create \
    --resource-group myNewResourceGroup \
    --template-file myResourceGroup.json
```

É melhor ler mais sobre como implantar a [partir de modelos](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Saiba como atualizar incrementalmente os ambientes, utilizar o ficheiro de parâmetros e aceder aos modelos a partir de um único local de armazenamento.

## <a name="next-steps"></a>Passos seguintes
Agora está pronto para começar a trabalhar com vários componentes de rede e VMs. Pode utilizar este ambiente de amostra para construir a sua aplicação utilizando os componentes centrais introduzidos aqui.
