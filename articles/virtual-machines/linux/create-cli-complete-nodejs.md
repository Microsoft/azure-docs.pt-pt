---
title: Criar um ambiente completo do Linux com a CLI clássica do Azure | Documentos da Microsoft
description: Crie armazenamento, uma VM do Linux, uma rede virtual e sub-rede, um balanceador de carga, uma NIC, um IP público e um grupo de segurança de rede, tudo a partir do zero com a CLI clássica do Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/09/2017
ms.author: cynthn
ms.openlocfilehash: 04c1d69fc46b9a918038e93c4fc56681f225d365
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58006222"
---
# <a name="create-a-complete-linux-environment-with-the-azure-classic-cli"></a>Criar um ambiente completo do Linux com a CLI clássica do Azure
Neste artigo, vamos criar uma rede simples com um balanceador de carga e um par de VMs que são úteis para desenvolvimento e a computação simples. Vamos percorrer o processo de comando ao comando, até ter duas VMs do Linux seguro e de trabalho para o qual pode ligar a partir em qualquer lugar na Internet. Em seguida, pode passar às redes mais complexas e de ambientes.

Ao longo do caminho, aprende sobre a hierarquia de dependências que oferece o modelo de implementação do Resource Manager e sobre qual a quantidade de energia fornece. Depois de ver como o sistema é criado, pode reconstrui-lo muito mais rapidamente usando [modelos Azure Resource Manager](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Além disso, depois de aprender como as partes do seu ambiente se encaixam, criar modelos para automatizá-las se torna mais fácil.

O ambiente contém:

* Duas VMs dentro de um conjunto de disponibilidade.
* Um balanceador de carga com uma regra de balanceamento de carga na porta 80.
* Regras de grupo (NSG) de segurança de rede para proteger a sua VM de tráfego indesejado.

Para criar este ambiente personalizado, tem a versão mais recente [CLI clássica do Azure](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) no modo Resource Manager (`azure config mode arm`). Terá também um ferramenta de análise de JSON. Este exemplo utiliza [jq](https://stedolan.github.io/jq/).


## <a name="cli-versions-to-complete-the-task"></a>Versões CLI para concluir a tarefa
Pode concluir a tarefa utilizando uma das seguintes versões CLI:

- [Azure CLI clássica](#quick-commands) – CLI para as clássica e de recursos gestão modelos de implementação (Este artigo)
- [CLI do Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) -CLI de próxima geração para o modelo de implementação de gestão de recursos


## <a name="quick-commands"></a>Comandos rápidos
Se precisar de realizar rapidamente a tarefa, os seguintes detalhes para a secção de segurança da base de comandos para carregar uma VM para o Azure. Mais informações detalhadas e contexto para cada etapa pode ser encontrada no resto do documento, a partir [aqui](#detailed-walkthrough).

Certifique-se de que tenha [a CLI clássica do Azure](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) conectado e utilizar o modo do Resource Manager:

```azurecli
azure config mode arm
```

Nos exemplos a seguir, substitua os nomes de parâmetros de exemplo pelos seus próprios valores. Os nomes de parâmetros de exemplo incluem `myResourceGroup`, `mystorageaccount`, e `myVM`.

Crie o grupo de recursos. O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na localização `westeurope`:

```azurecli
azure group create -n myResourceGroup -l westeurope
```

Verifique se o grupo de recursos ao utilizar o analisador JSON:

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Crie a conta de armazenamento. O exemplo seguinte cria uma conta de armazenamento com o nome `mystorageaccount`. (O nome da conta de armazenamento tem de ser exclusivo, então, fornecer seu próprio nome exclusivo.)

```azurecli
azure storage account create -g myResourceGroup -l westeurope \
  --kind Storage --sku-name GRS mystorageaccount
```

Certifique-se a conta de armazenamento ao utilizar o analisador JSON:

```azurecli
azure storage account show -g myResourceGroup mystorageaccount --json | jq '.'
```

Criar a rede virtual. O exemplo seguinte cria uma rede virtual denominada `myVnet`:

```azurecli
azure network vnet create -g myResourceGroup -l westeurope\
  -n myVnet -a 192.168.0.0/16
```

Crie uma sub-rede. O exemplo seguinte cria uma sub-rede denominada `mySubnet`:

```azurecli
azure network vnet subnet create -g myResourceGroup \
  -e myVnet -n mySubnet -a 192.168.1.0/24
```

Verifique se a rede virtual e sub-rede, ao utilizar o analisador JSON:

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Crie um IP público. O exemplo seguinte cria um IP público com o nome `myPublicIP` com o nome DNS de `mypublicdns`. (O nome DNS tem de ser exclusivo, então, fornecer seu próprio nome exclusivo.)

```azurecli
azure network public-ip create -g myResourceGroup -l westeurope \
  -n myPublicIP  -d mypublicdns -a static -i 4
```

Crie o Balanceador de carga. O exemplo seguinte cria um balanceador de carga com o nome `myLoadBalancer`:

```azurecli
azure network lb create -g myResourceGroup -l westeurope -n myLoadBalancer
```

Criar um conjunto IP Front-end do Balanceador de carga e associar o IP público. O exemplo seguinte cria um conjunto IP de front-end com o nome `mySubnetPool`:

```azurecli
azure network lb frontend-ip create -g myResourceGroup -l myLoadBalancer \
  -i myPublicIP -n myFrontEndPool
```

Crie o conjunto IP de back-end do Balanceador de carga. O exemplo seguinte cria um conjunto IP de back-end com o nome `myBackEndPool`:

```azurecli
azure network lb address-pool create -g myResourceGroup -l myLoadBalancer \
  -n myBackEndPool
```

Crie regras de tradução (NAT) de endereço para o Balanceador de carga de rede SSH de entrada. O exemplo seguinte cria duas regras de Balanceador de carga, `myLoadBalancerRuleSSH1` e `myLoadBalancerRuleSSH2`:

```azurecli
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH1 -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH2 -p tcp -f 4223 -b 22
```

Criar web regras NAT para o Balanceador de carga de entrada. O exemplo seguinte cria uma regra de Balanceador de carga com o nome `myLoadBalancerRuleWeb`:

```azurecli
azure network lb rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleWeb -p tcp -f 80 -b 80 \
  -t myFrontEndPool -o myBackEndPool
```

Crie a sonda de estado de funcionamento do Balanceador de carga. O exemplo seguinte cria uma sonda TCP com o nome `myHealthProbe`:

```azurecli
azure network lb probe create -g myResourceGroup -l myLoadBalancer \
  -n myHealthProbe -p "tcp" -i 15 -c 4
```

Verifique se o Balanceador de carga, conjuntos de IP e as regras NAT ao utilizar o analisador JSON:

```azurecli
azure network lb show -g myResourceGroup -n myLoadBalancer --json | jq '.'
```

Crie a primeira placa de interface de rede (NIC). Substitua o `#####-###-###` secções pelo seu ID de subscrição do Azure. A sua subscrição indicado na saída do ID **jq** quando examinar os recursos que está a criar. Também pode ver o seu ID de subscrição com `azure account list`.

O exemplo seguinte cria um NIC com o nome `myNic1`:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic1 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
```

Criar a segunda NIC. O exemplo seguinte cria um NIC com o nome `myNic2`:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic2 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
```

Verifique se os dois NICs ao utilizar o analisador JSON:

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
azure network nic show myResourceGroup myNic2 --json | jq '.'
```

Crie o grupo de segurança de rede. O exemplo seguinte cria um grupo de segurança de rede com o nome `myNetworkSecurityGroup`:

```azurecli
azure network nsg create -g myResourceGroup -l westeurope \
  -n myNetworkSecurityGroup
```

Adicione duas regras de entrada para o grupo de segurança de rede. O exemplo seguinte cria duas regras, `myNetworkSecurityGroupRuleSSH` e `myNetworkSecurityGroupRuleHTTP`:

```azurecli
azure network nsg rule create -p tcp -r inbound -y 1000 -u 22 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleSSH
azure network nsg rule create -p tcp -r inbound -y 1001 -u 80 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleHTTP
```

Verifique se o grupo de segurança de rede e regras de entrada, ao utilizar o analisador JSON:

```azurecli
azure network nsg show -g myResourceGroup -n myNetworkSecurityGroup --json | jq '.'
```

Vincule o grupo de segurança de rede para os dois NICs:

```azurecli
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic1
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic2
```

Crie o conjunto de disponibilidade. O exemplo seguinte cria um conjunto de disponibilidade designado `myAvailabilitySet`:

```azurecli
azure availset create -g myResourceGroup -l westeurope -n myAvailabilitySet
```

Crie a primeira VM do Linux. O exemplo seguinte cria uma VM com o nome `myVM1`:

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic1 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Crie a segunda VM do Linux. O exemplo seguinte cria uma VM com o nome `myVM2`:

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic2 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Utilize o analisador JSON para verificar se o tudo o que foi criado:

```azurecli
azure vm show -g myResourceGroup -n myVM1 --json | jq '.'
azure vm show -g myResourceGroup -n myVM2 --json | jq '.'
```

Exporte o seu novo ambiente para um modelo para voltar a criar novas instâncias rapidamente:

```azurecli
azure group export myResourceGroup
```

## <a name="detailed-walkthrough"></a>Instruções detalhadas
Os passos detalhados que se seguem explicam o que cada comando está fazendo à medida que cria seu ambiente. Esses conceitos são úteis quando cria seus próprios ambientes personalizados para desenvolvimento ou produção.

Certifique-se de que tenha [a CLI clássica do Azure](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) conectado e utilizar o modo do Resource Manager:

```azurecli
azure config mode arm
```

Nos exemplos a seguir, substitua os nomes de parâmetros de exemplo pelos seus próprios valores. Os nomes de parâmetros de exemplo incluem `myResourceGroup`, `mystorageaccount`, e `myVM`.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Criar grupos de recursos e selecione as localizações de implementação
Os grupos de recursos do Azure são entidades de lógica de implementação que contêm informações de configuração e metadados para ativar a lógica de gestão de implementações de recurso. O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na localização `westeurope`:

```azurecli
azure group create --name myResourceGroup --location westeurope
```

Saída:

```azurecli                        
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
Terá de contas de armazenamento para os discos da VM e para quaisquer discos de dados adicionais que pretende adicionar. Criar contas de armazenamento quase que imediatamente depois de criar grupos de recursos.

Aqui, vamos utilizar o `azure storage account create` de comando, passando a localização da conta, o grupo de recursos que controla e o tipo de suporte de armazenamento que pretende. O exemplo seguinte cria uma conta de armazenamento com o nome `mystorageaccount`:

```azurecli
azure storage account create \  
  --location westeurope \
  --resource-group myResourceGroup \
  --kind Storage --sku-name GRS \
  mystorageaccount
```

Saída:

```azurecli
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
```

Para examinar nosso grupo de recursos, utilizando o `azure group show` comando este problema, vamos utilizar o [jq](https://stedolan.github.io/jq/) ferramenta juntamente com o `--json` opção de CLI do Azure. (Pode usar **jsawk** ou qualquer biblioteca de linguagem que preferir para analisar o JSON.)

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Saída:

```json
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Para investigar a conta de armazenamento com a CLI, tem primeiro de definir os nomes de conta e chaves. Substitua o nome da conta de armazenamento no exemplo a seguir com um nome que escolher:

```bash
export AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show mystorageaccount --resource-group myResourceGroup --json | jq -r '.string')"
```

Em seguida, pode ver facilmente as informações de armazenamento:

```azurecli
azure storage container list
```

Saída:

```azurecli
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## <a name="create-a-virtual-network-and-subnet"></a>Criar uma rede virtual e uma sub-rede
Em seguida, vai precisar de criar uma rede virtual em execução no Azure e uma sub-rede na qual pode criar as suas VMs. O exemplo seguinte cria uma rede virtual denominada `myVnet` com o `192.168.0.0/16` prefixo de endereço:

```azurecli
azure network vnet create --resource-group myResourceGroup --location westeurope \
  --name myVnet --address-prefixes 192.168.0.0/16
```

Saída:

```azurecli
info:    Executing command network vnet create
+ Looking up virtual network "myVnet"
+ Creating virtual network "myVnet"
+ Loading virtual network state
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet
data:    Name                            : myVnet
data:    Type                            : Microsoft.Network/virtualNetworks
data:    Location                        : westeurope
data:    ProvisioningState               : Succeeded
data:    Address prefixes:
data:      192.168.0.0/16
info:    network vnet create command OK
```

Mais uma vez, vamos utilizar a opção – json de `azure group show` e `jq` para ver como estamos a criar nossos recursos. Agora, temos uma `storageAccounts` recursos e um `virtualNetworks` recursos.  

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Saída:

```json
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
      "name": "myVnet",
      "type": "virtualNetworks",
      "location": "westeurope",
      "tags": null
    },
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Agora vamos criar uma sub-rede no `myVnet` rede virtual na qual as VMs são implementadas. Vamos utilizar o `azure network vnet subnet create` comando, juntamente com os recursos já criamos: a `myResourceGroup` grupo de recursos e o `myVnet` rede virtual. No exemplo a seguir, podemos adicionar a sub-rede denominada `mySubnet` com o prefixo de endereço de sub-rede de `192.168.1.0/24`:

```azurecli
azure network vnet subnet create --resource-group myResourceGroup \
  --vnet-name myVnet --name mySubnet --address-prefix 192.168.1.0/24
```

Saída:

```azurecli
info:    Executing command network vnet subnet create
+ Looking up the subnet "mySubnet"
+ Creating subnet "mySubnet"
+ Looking up the subnet "mySubnet"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:    Type                            : Microsoft.Network/virtualNetworks/subnets
data:    ProvisioningState               : Succeeded
data:    Name                            : mySubnet
data:    Address prefix                  : 192.168.1.0/24
data:
info:    network vnet subnet create command OK
```

Como a sub-rede é logicamente dentro da rede virtual, vamos ver as informações de sub-rede com um comando ligeiramente diferente. É o comando usamos `azure network vnet show`, mas vamos continuar a examinar a saída JSON utilizando `jq`.

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Saída:

```json
{
  "subnets": [
    {
      "ipConfigurations": [],
      "addressPrefix": "192.168.1.0/24",
      "provisioningState": "Succeeded",
      "name": "mySubnet",
      "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
    }
  ],
  "tags": {},
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "provisioningState": "Succeeded",
  "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "name": "myVnet",
  "location": "westeurope"
}
```

## <a name="create-a-public-ip-address"></a>Crie um endereço IP público
Agora vamos criar o endereço IP público (PIP) que Atribuímos à seu Balanceador de carga. Permite-lhe ligar às suas VMs a partir da Internet, utilizando o `azure network public-ip create` comando. Como o endereço predefinido é dinâmico, podemos criar uma entrada DNS com o nome no **cloudapp.azure.com** domínio utilizando o `--domain-name-label` opção. O exemplo seguinte cria um IP público com o nome `myPublicIP` com o nome DNS de `mypublicdns`. Uma vez que o nome DNS tem de ser exclusivo, fornece o nome DNS exclusivo:

```azurecli
azure network public-ip create --resource-group myResourceGroup \
  --location westeurope --name myPublicIP --domain-name-label mypublicdns
```

Saída:

```azurecli
info:    Executing command network public-ip create
+ Looking up the public ip "myPublicIP"
+ Creating public ip address "myPublicIP"
+ Looking up the public ip "myPublicIP"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
data:    Name                            : myPublicIP
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    Idle timeout                    : 4
data:    Domain name label               : mypublicdns
data:    FQDN                            : mypublicdns.westeurope.cloudapp.azure.com
info:    network public-ip create command OK
```

O endereço IP público também é um recurso de nível superior, para que possa ver com `azure group show`.

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Saída:

```json
{
"tags": {},
"id": "/subscriptions/guid/resourceGroups/myResourceGroup",
"name": "myResourceGroup",
"provisioningState": "Succeeded",
"location": "westeurope",
"properties": {
    "provisioningState": "Succeeded"
},
"resources": [
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "name": "myPublicIP",
    "type": "publicIPAddresses",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
    "name": "myVnet",
    "type": "virtualNetworks",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "name": "mystorageaccount",
    "type": "storageAccounts",
    "location": "westeurope",
    "tags": null
    }
],
"permissions": [
    {
    "actions": [
        "*"
    ],
    "notActions": []
    }
]
}
```

Pode investigar mais detalhes de recursos, incluindo o nome de domínio completamente qualificado (FQDN) do subdomínio, utilizando o completa `azure network public-ip show` comando. O recurso de endereço IP público foi alocado logicamente, mas ainda não foi atribuído um endereço específico. Para obter um endereço IP, vai precisar de um balanceador de carga, podemos ainda não criou.

```azurecli
azure network public-ip show myResourceGroup myPublicIP --json | jq '.'
```

Saída:

```json
{
"tags": {},
"publicIpAllocationMethod": "Dynamic",
"dnsSettings": {
    "domainNameLabel": "mypublicdns",
    "fqdn": "mypublicdns.westeurope.cloudapp.azure.com"
},
"idleTimeoutInMinutes": 4,
"provisioningState": "Succeeded",
"etag": "W/\"c63154b3-1130-49b9-a887-877d74d5ebc5\"",
"id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
"name": "myPublicIP",
"location": "westeurope"
}
```

## <a name="create-a-load-balancer-and-ip-pools"></a>Criar um balanceador de carga e conjuntos IP
Quando cria um balanceador de carga, permite-lhe distribuir o tráfego por várias VMs. Ele também fornece redundância à sua aplicação ao executar várias VMs que respondem a pedidos de utilizador em caso de manutenção ou cargas pesadas. O exemplo seguinte cria um balanceador de carga com o nome `myLoadBalancer`:

```azurecli
azure network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer
```

Saída:

```azurecli
info:    Executing command network lb create
+ Looking up the load balancer "myLoadBalancer"
+ Creating load balancer "myLoadBalancer"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer
data:    Name                            : myLoadBalancer
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
info:    network lb create command OK
```

Nosso Balanceador de carga é bastante vazio, então, vamos criar alguns conjuntos de IP. Queremos criar dois conjuntos IP para nosso Balanceador de carga, uma para o front-end e outra para o back-end. O conjunto IP Front-end é publicamente visível. Também é a localização à qual Atribuímos o PIP que criámos anteriormente. Em seguida, usamos o conjunto de back-end como uma localização para nossas VMs para ligar a. Dessa forma, o tráfego pode fluir através do Balanceador de carga para as VMs.

Em primeiro lugar, vamos criar nosso conjunto IP Front-end. O exemplo seguinte cria um conjunto de front-end com o nome `myFrontEndPool`:

```azurecli
azure network lb frontend-ip create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --public-ip-name myPublicIP \
  --name myFrontEndPool
```

Saída:

```azurecli
info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "myLoadBalancer"
+ Looking up the public ip "myPublicIP"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myFrontEndPool
data:    Provisioning state              : Succeeded
data:    Private IP allocation method    : Dynamic
data:    Public IP address id            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
info:    network lb mySubnet-ip create command OK
```

Observe como usamos o `--public-ip-name` comutador para transmitir o `myPublicIP` que criámos anteriormente. Atribuir o endereço IP público para o Balanceador de carga permite-lhe alcançar as suas VMs através da Internet.

Em seguida, vamos criar nosso segundo conjunto IP, desta vez para o nosso tráfego de back-end. O exemplo seguinte cria um conjunto de back-end com o nome `myBackEndPool`:

```azurecli
azure network lb address-pool create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myBackEndPool
```

Saída:

```azurecli
info:    Executing command network lb address-pool create
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

Podemos ver nossa Balanceador de carga de estado de funcionamento ao consultar com `azure network lb show` e examinar a saída JSON:

```azurecli
azure network lb show myResourceGroup myLoadBalancer --json | jq '.'
```

Saída:

```json
{
  "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [],
  "probes": []
}
```

## <a name="create-load-balancer-nat-rules"></a>Criar regras NAT de Balanceador de carga
Para obter o tráfego que flui através do nosso Balanceador de carga, é necessário criar regras de tradução (NAT) que especificam as ações de entrada ou saídas de endereço de rede. Pode especificar o protocolo a utilizar e depois mapeiam portas externas para as portas internas conforme pretendido. Para o nosso ambiente, vamos criar algumas regras que permitem que o SSH através do nosso Balanceador de carga às nossas VMs. Configuramos as portas TCP 4222 e 4223 para direcionar para a porta TCP 22 em nossas VMs (o que podemos criar mais tarde). O exemplo seguinte cria uma regra com o nome `myLoadBalancerRuleSSH1` para mapear a porta TCP 4222 para a porta 22:

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 \
  --protocol tcp --frontend-port 4222 --backend-port 22
```

Saída:

```azurecli
info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default enable floating ip: false
warn:    Using default idle timeout: 4
warn:    Using default mySubnet IP configuration "myFrontEndPool"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleSSH1
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 4222
data:    Backend port                    : 22
data:    Enable floating IP              : false
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
info:    network lb inbound-nat-rule create command OK
```

Repita o procedimento para a segunda regra NAT para SSH. O exemplo seguinte cria uma regra com o nome `myLoadBalancerRuleSSH2` para mapear a porta TCP 4223 para a porta 22:

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22
```

Vamos também posso continuar e criar uma regra NAT para a porta TCP 80 para tráfego da web, vinculando a regra até nossos conjuntos IP. Se a regra para um conjunto IP, em vez de conectar a regra às nossas VMs individualmente, interligamos os, pode adicionar ou remover as VMs do conjunto IP. O Balanceador de carga se ajusta automaticamente o fluxo de tráfego. O exemplo seguinte cria uma regra com o nome `myLoadBalancerRuleWeb` para mapear a porta TCP 80 para a porta 80:

```azurecli
azure network lb rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleWeb --protocol tcp \
  --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-address-pool-name myBackEndPool
```

Saída:

```azurecli
info:    Executing command network lb rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default idle timeout: 4
warn:    Using default enable floating ip: false
warn:    Using default load distribution: Default
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleWeb
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 80
data:    Backend port                    : 80
data:    Enable floating IP              : false
data:    Load distribution               : Default
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
data:    Backend address pool id         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
info:    network lb rule create command OK
```

## <a name="create-a-load-balancer-health-probe"></a>Criar uma sonda de estado de funcionamento do balanceador de carga
Um Estado de funcionamento de sonda periodicamente verificações nas VMs atrás de nossa Balanceador de carga para se certificar-se de que estão a funcionar e a responder a pedidos, tal como definido. Caso contrário, que foram removidos do operação para se certificar de que os utilizadores não estão a ser direcionados aos mesmos. Pode definir verificações personalizadas para a sonda de estado de funcionamento, juntamente com intervalos e valores de tempo limite. Para obter mais informações sobre as sondas de estado de funcionamento, consulte [sondas do Balanceador de carga](../../load-balancer/load-balancer-custom-probe-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). O exemplo seguinte cria um TCP estado de funcionamento sondado nomeado `myHealthProbe`:

```azurecli
azure network lb probe create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myHealthProbe --protocol "tcp" \
  --interval 15 --count 4
```

Saída:

```azurecli
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myHealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

Aqui, especificamos um intervalo de 15 segundos de nossas verificações de estado de funcionamento. Pode perdermos um máximo de quatro sondas (um minuto) antes do Balanceador de carga considera que o anfitrião já não está a funcionar.

## <a name="verify-the-load-balancer"></a>Verifique se o Balanceador de carga
Agora a configuração de Balanceador de carga é feita. Aqui estão as etapas realizadas:

1. Criou um balanceador de carga.
2. Criou um conjunto IP de front-end e atribuídos um IP público ao mesmo.
3. Criou um conjunto IP de back-end que se podem ligar as VMs.
4. Criar regras NAT que permitam o SSH para as VMs para gestão, juntamente com uma regra que permita a porta TCP 80 para a nossa aplicação web.
5. Adicionou uma sonda de estado de funcionamento para verificar periodicamente as VMs. Esta pesquisa de estado de funcionamento garante que os utilizadores não tentam aceder uma VM que já não está a funcionar ou a servir de conteúdo.

Vamos rever o seu Balanceador de carga aparência agora:

```azurecli
azure network lb show --resource-group myResourceGroup \
  --name myLoadBalancer --json | jq '.'
```

Saída:

```json
{
  "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH1",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4222,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    },
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH2",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4223,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "inboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        },
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleWeb",
      "provisioningState": "Succeeded",
      "enableFloatingIP": false,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "backendAddressPool": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
      },
      "protocol": "Tcp",
      "loadDistribution": "Default",
      "mySubnetPort": 80,
      "backendPort": 80,
      "idleTimeoutInMinutes": 4
    }
  ],
  "probes": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myHealthProbe",
      "provisioningState": "Succeeded",
      "numberOfProbes": 4,
      "intervalInSeconds": 15,
      "port": 80,
      "protocol": "Tcp",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe"
    }
  ]
}
```

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Crie um NIC para utilizar com a VM do Linux
NICs estão disponíveis por meio de programação porque pode aplicar regras de seu uso. Também pode ter mais do que um. A seguir `azure network nic create` de comando, conectar o NIC para o conjunto IP de back-end de carga e associá-la com a regra NAT para permitir o tráfego SSH.

Substitua o `#####-###-###` secções pelo seu ID de subscrição do Azure. Sua assinatura ID indicado na saída do `jq` quando examinar os recursos que está a criar. Também pode ver o seu ID de subscrição com `azure account list`.

O exemplo seguinte cria um NIC com o nome `myNic1`:

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 \
  --lb-address-pool-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
```

Saída:

```azurecli
info:    Executing command network nic create
+ Looking up the subnet "mySubnet"
+ Looking up the network interface "myNic1"
+ Creating network interface "myNic1"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1
data:    Name                            : myNic1
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : Nic-IP-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:      Load balancer backend address pools:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
data:      Load balancer inbound NAT rules:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
data:
info:    network nic create command OK
```

Pode ver os detalhes, examinando o recurso diretamente. Examine o recurso utilizando o `azure network nic show` comando:

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
```

Saída:

```json
{
  "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
  "provisioningState": "Succeeded",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1",
  "name": "myNic1",
  "type": "Microsoft.Network/networkInterfaces",
  "location": "westeurope",
  "ipConfigurations": [
    {
      "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/Nic-IP-config",
      "loadBalancerBackendAddressPools": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
        }
      ],
      "loadBalancerInboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        }
      ],
      "privateIPAddress": "192.168.1.4",
      "privateIPAllocationMethod": "Dynamic",
      "subnet": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
      },
      "provisioningState": "Succeeded",
      "name": "Nic-IP-config"
    }
  ],
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": []
  },
  "enableIPForwarding": false,
  "resourceGuid": "a20258b8-6361-45f6-b1b4-27ffed28798c"
}
```

Agora podemos criar a segunda NIC, conectando-se ao nosso conjunto IP de back-end novamente. Esta regra NAT de tempo a segunda permite tráfego SSH. O exemplo seguinte cria um NIC com o nome `myNic2`:

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 \
  --lb-address-pool-ids  /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2
```

## <a name="create-a-network-security-group-and-rules"></a>Criar um grupo de segurança de rede e regras
Agora vamos criar um grupo de segurança de rede e as regras de entrada que regem o acesso para o NIC. Um grupo de segurança de rede pode ser aplicado a um NIC ou uma sub-rede. Pode definir regras para controlar o fluxo de tráfego que entra e sai as suas VMs. O exemplo seguinte cria um grupo de segurança de rede com o nome `myNetworkSecurityGroup`:

```azurecli
azure network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Vamos adicionar a regra de entrada para o NSG permitir ligações de entrada na porta 22 (para suportar SSH). O exemplo seguinte cria uma regra com o nome `myNetworkSecurityGroupRuleSSH` para permitir o TCP na porta 22:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1000 --destination-port-range 22 --access allow \
  --name myNetworkSecurityGroupRuleSSH
```

Agora vamos adicionar a regra de entrada para o NSG permitir ligações de entrada na porta 80 (para suportar o tráfego da web). O exemplo seguinte cria uma regra com o nome `myNetworkSecurityGroupRuleHTTP` para permitir o TCP na porta 80:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1001 --destination-port-range 80 --access allow \
  --name myNetworkSecurityGroupRuleHTTP
```

> [!NOTE]
> A regra de entrada é um filtro para ligações de rede de entrada. Neste exemplo, ligamos o NSG para a NIC virtual de VMs, que significa que qualquer pedido para a porta 22 passado para o NIC na nossa VM. Esta regra de entrada é sobre uma ligação de rede, e não sobre um ponto de extremidade, que é o que seria sobre em implementações clássicas. Para abrir uma porta, tem de deixar os `--source-port-range` definido como '\*' (o valor predefinido) para aceitar pedidos de entrada de **qualquer** solicitando a porta. As portas são normalmente dinâmicas.
>
>

## <a name="bind-to-the-nic"></a>Vincular ao NIC
Associar o NSG para NICs. É necessário ligar a nossa NICs com nosso grupo de segurança de rede. Execute os dois comandos, para conectar os dois de nossos NICs:

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic1 \
  --network-security-group-name myNetworkSecurityGroup
```

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic2 \
  --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade
Conjuntos de disponibilidade ajuda spread as suas VMs em domínios de falha e domínios de atualização. Vamos criar um conjunto de disponibilidade para as suas VMs. O exemplo seguinte cria um conjunto de disponibilidade designado `myAvailabilitySet`:

```azurecli
azure availset create --resource-group myResourceGroup --location westeurope
  --name myAvailabilitySet
```

Domínios de falha definem um agrupamento de máquinas virtuais que partilham um comutador de rede e fonte de energia comum. Por predefinição, as máquinas virtuais que estejam configuradas no seu conjunto de disponibilidade estão separadas por até três domínios de falha. A idéia é que um problema de hardware em um destes domínios de falhas não afeta todas as VMS que está a executar a aplicação. Azure distribui automaticamente as VMs em domínios de falha quando colocá-las num conjunto de disponibilidade.

Domínios de atualização indicam os grupos de máquinas virtuais e o hardware físico subjacente que pode ser reiniciado ao mesmo tempo. A ordem em que os domínios de atualização são reiniciados não pode ser sequencial durante a manutenção planeada, mas apenas uma atualização é reiniciada de cada vez. Novamente, Azure distribui automaticamente as suas VMs por domínios de atualização quando colocando-os num site de disponibilidade.

Leia mais sobre [o gerenciamento de VMs](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="create-the-linux-vms"></a>Criar as VMs do Linux
Acabou de criar os recursos de armazenamento e de rede para suportar VMs acessível pela Internet. Agora vamos criar as VMs e protegê-los com uma chave SSH, que não tem uma palavra-passe. Neste caso, vamos criar um VM com base no mais recente LTS do Ubuntu. Vamos localizar essa informação de imagem usando `azure vm image list`, conforme descrito na [encontrar imagens de VM do Azure](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Selecionamos uma imagem usando o comando `azure vm image list westeurope canonical | grep LTS`. Neste caso, usamos `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`. Para o último campo, passamos `latest` , de forma que no futuro, podemos obter sempre a versão mais recente. (É a cadeia de caracteres usamos `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`).

Este passo seguinte é que qualquer pessoa que já criou um ssh chave públicas e privadas de rsa emparelhar no Linux ou Mac utilizando **ssh-keygen - t rsa -b 2048**. Se não tiver qualquer pares de chaves de certificado seu `~/.ssh` diretório, pode criá-los:

* Automaticamente, usando o `azure vm create --generate-ssh-keys` opção.
* Manualmente, usando [as instruções para criá-los por conta própria](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Em alternativa, pode utilizar o `--admin-password` métodos para autenticar as ligações SSH depois da VM é criada. Este método é geralmente menos seguro.

Vamos criar a VM ao trazer todos os nossos recursos e informações em conjunto com o `azure vm create` comando:

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM1 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic1 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username azureuser
```

Saída:

```azurecli
info:    Executing command vm create
+ Looking up the VM "myVM1"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account mystorageaccount
+ Looking up the availability set "myAvailabilitySet"
info:    Found an Availability set "myAvailabilitySet"
+ Looking up the NIC "myNic1"
info:    Found an existing NIC "myNic1"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet" in the NIC "myNic1"
info:    This is an NIC without publicIP configured
info:    The storage URI 'https://mystorageaccount.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
info:    vm create command OK
```

Pode ligar à sua VM imediatamente ao utilizar as chaves SSH predefinidas. Certifique-se de que especifica a porta adequada, uma vez que estamos passando pelo balanceador de carga. (Para nossa primeira VM, configuramos a regra NAT para reencaminhar porta 4222 para a VM.)

```bash
ssh ops@mypublicdns.westeurope.cloudapp.azure.com -p 4222
```

Saída:

```bash
The authenticity of host '[mypublicdns.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[mypublicdns.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-34-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    https://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

ops@myVM1:~$
```

Vá em frente e criar a sua segunda VM da mesma forma:

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM2 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic2 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username azureuser
```

E agora, pode utilizar o `azure vm show myResourceGroup myVM1` comando para examinar o que criou. Neste momento, está a executar as VMs do Ubuntu atrás de um balanceador de carga do Azure que pode iniciar sessão em apenas com o par de chaves SSH (porque as palavras-passe estão desativadas). Pode instalar o nginx ou httpd, implementar uma aplicação web e ver o tráfego de fluxo através do Balanceador de carga para ambas as VMs.

```azurecli
azure vm show --resource-group myResourceGroup --name myVM1
```

Saída:

```azurecli
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "myNic1"
data:    Id                              :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :myVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :16.04.0-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clib45a8b650f4428a1-os-1471973896525
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://mystorageaccount.blob.core.windows.net/vhds/clib45a8b650f4428a1-os-1471973896525.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM1
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-24-D4-AA
data:          Provisioning State        :Succeeded
data:          Name                      :LmyNic1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://mystorageaccount.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK

```


## <a name="export-the-environment-as-a-template"></a>Exportar o ambiente como um modelo
Agora que criou neste ambiente, e se quiser criar um ambiente de desenvolvimento adicional com os mesmos parâmetros, ou a um ambiente de produção que o faça corresponder? Gestor de recursos usa os modelos JSON que definem a todos os parâmetros para o seu ambiente. Crie os ambientes completos fazendo referência este modelo JSON. Pode [criar manualmente os modelos JSON](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou exportar um ambiente existente para criar o modelo JSON para:

```azurecli
azure group export --name myResourceGroup
```

Este comando cria o `myResourceGroup.json` ficheiro no diretório de trabalho atual. Quando criar um ambiente a partir deste modelo, são-lhe pedido para todos os nomes de recursos, incluindo os nomes para o Balanceador de carga, interfaces de rede ou VMs. Pode preencher esses nomes em seu arquivo de modelo ao adicionar o `-p` ou `--includeParameterDefaultValue` parâmetro para o `azure group export` comando que foi mostrado anteriormente. Editar o modelo JSON para especificar os nomes de recursos, ou [crie um ficheiro Parameters. JSON](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) que especifica os nomes de recursos.

Para criar um ambiente a partir do seu modelo:

```azurecli
azure group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

Pode querer ler [mais sobre como implementar a partir de modelos](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Saiba mais sobre como atualizar ambientes incrementalmente, utilize o ficheiro de parâmetros e modelos de acesso a partir de uma localização de armazenamento única.

## <a name="next-steps"></a>Passos Seguintes
Agora, está pronto para começar a trabalhar com vários componentes de rede e as VMs. Pode utilizar este ambiente de exemplo para criar seu aplicativo usando os componentes nucleares apresentados aqui.
