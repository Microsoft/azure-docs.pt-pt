---
title: Criar um ambiente Linux completo com a CLI clássica do Azure
description: Criar armazenamento, uma VM do Linux, uma rede virtual e uma sub-rede, um balanceador de carga, uma NIC, um IP público e um grupo de segurança de rede, tudo do zero usando a CLI clássica do Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/09/2017
ms.author: cynthn
ms.openlocfilehash: 1ee89ce18600685f3f82bfb49d4d8ecbaf192b04
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036519"
---
# <a name="create-a-complete-linux-environment-with-the-azure-classic-cli"></a>Criar um ambiente Linux completo com a CLI clássica do Azure
Neste artigo, criamos uma rede simples com um balanceador de carga e um par de VMs que são úteis para o desenvolvimento e a computação simples. Percorremos o comando Process pelo comando, até que você tenha duas VMs Linux em funcionamento para as quais você pode se conectar de qualquer lugar na Internet. Em seguida, você pode passar para redes e ambientes mais complexos.

Ao longo do caminho, você aprende sobre a hierarquia de dependência que o modelo de implantação do Gerenciador de recursos fornece e sobre quanto energia ele oferece. Depois de ver como o sistema é criado, você pode recriá-lo muito mais rapidamente usando [modelos de Azure Resource Manager](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Além disso, depois de aprender como as partes do seu ambiente se encaixam, a criação de modelos para automatizar a ti se torna mais fácil.

O ambiente contém:

* Duas VMs dentro de um conjunto de disponibilidade.
* Um balanceador de carga com uma regra de balanceamento de carga na porta 80.
* Regras de NSG (grupo de segurança de rede) para proteger sua VM contra tráfego indesejado.

Para criar esse ambiente personalizado, você precisa da [CLI clássica do Azure](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) mais recente no modo do Gerenciador de recursos (`azure config mode arm`). Você também precisa de uma ferramenta de análise de JSON. Este exemplo usa [JQ](https://stedolan.github.io/jq/).


## <a name="cli-versions-to-complete-the-task"></a>Versões CLI para concluir a tarefa
Pode concluir a tarefa utilizando uma das seguintes versões CLI:

- [CLI clássica do Azure](#quick-commands) – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos (este artigo)
- [CLI do Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) -nossa CLI da próxima geração para o modelo de implantação do gerenciamento de recursos


## <a name="quick-commands"></a>Comandos rápidos
Se você precisar realizar rapidamente a tarefa, a seção a seguir detalha os comandos de base para carregar uma VM no Azure. Informações mais detalhadas e contexto para cada etapa podem ser encontrados no restante do documento, começando [aqui](#detailed-walkthrough).

Verifique se você tem [a CLI clássica do Azure](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) conectada e usando o modo do Resource Manager:

```azurecli
azure config mode arm
```

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo pelos seus próprios valores. Os nomes de parâmetro de exemplo incluem `myResourceGroup`, `mystorageaccount`e `myVM`.

Crie o grupo de recursos. O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na localização `westeurope`:

```azurecli
azure group create -n myResourceGroup -l westeurope
```

Verifique o grupo de recursos usando o analisador JSON:

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Crie a conta de armazenamento. O exemplo a seguir cria uma conta de armazenamento chamada `mystorageaccount`. (O nome da conta de armazenamento deve ser exclusivo, portanto, forneça seu próprio nome exclusivo.)

```azurecli
azure storage account create -g myResourceGroup -l westeurope \
  --kind Storage --sku-name GRS mystorageaccount
```

Verifique a conta de armazenamento usando o analisador JSON:

```azurecli
azure storage account show -g myResourceGroup mystorageaccount --json | jq '.'
```

Criar a rede virtual. O exemplo a seguir cria uma rede virtual chamada `myVnet`:

```azurecli
azure network vnet create -g myResourceGroup -l westeurope\
  -n myVnet -a 192.168.0.0/16
```

Crie uma sub-rede. O exemplo a seguir cria uma sub-rede chamada `mySubnet`:

```azurecli
azure network vnet subnet create -g myResourceGroup \
  -e myVnet -n mySubnet -a 192.168.1.0/24
```

Verifique a rede virtual e a sub-rede usando o analisador JSON:

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Crie um IP público. O exemplo a seguir cria um IP público chamado `myPublicIP` com o nome DNS de `mypublicdns`. (O nome DNS deve ser exclusivo, portanto, forneça seu próprio nome exclusivo.)

```azurecli
azure network public-ip create -g myResourceGroup -l westeurope \
  -n myPublicIP  -d mypublicdns -a static -i 4
```

Crie o balanceador de carga. O exemplo a seguir cria um balanceador de carga chamado `myLoadBalancer`:

```azurecli
azure network lb create -g myResourceGroup -l westeurope -n myLoadBalancer
```

Crie um pool de IPS de front-end para o balanceador de carga e associe o IP público. O exemplo a seguir cria um pool de IPS de front-end chamado `mySubnetPool`:

```azurecli
azure network lb frontend-ip create -g myResourceGroup -l myLoadBalancer \
  -i myPublicIP -n myFrontEndPool
```

Crie o pool de IPS de back-end para o balanceador de carga. O exemplo a seguir cria um pool de IPS de back-end chamado `myBackEndPool`:

```azurecli
azure network lb address-pool create -g myResourceGroup -l myLoadBalancer \
  -n myBackEndPool
```

Crie regras de NAT (conversão de endereços de rede) de entrada SSH para o balanceador de carga. O exemplo a seguir cria duas regras de balanceador de carga, `myLoadBalancerRuleSSH1` e `myLoadBalancerRuleSSH2`:

```azurecli
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH1 -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH2 -p tcp -f 4223 -b 22
```

Crie as regras NAT de entrada da Web para o balanceador de carga. O exemplo a seguir cria uma regra de balanceador de carga chamada `myLoadBalancerRuleWeb`:

```azurecli
azure network lb rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleWeb -p tcp -f 80 -b 80 \
  -t myFrontEndPool -o myBackEndPool
```

Crie a investigação de integridade do balanceador de carga. O exemplo a seguir cria uma investigação TCP chamada `myHealthProbe`:

```azurecli
azure network lb probe create -g myResourceGroup -l myLoadBalancer \
  -n myHealthProbe -p "tcp" -i 15 -c 4
```

Verifique o balanceador de carga, os pools de IPS e as regras de NAT usando o analisador JSON:

```azurecli
azure network lb show -g myResourceGroup -n myLoadBalancer --json | jq '.'
```

Crie a primeira NIC (placa de interface de rede). Substitua as seções `#####-###-###` pela sua própria ID de assinatura do Azure. Sua ID de assinatura é observada na saída de **JQ** quando você examina os recursos que está criando. Você também pode exibir sua ID de assinatura com `azure account list`.

O exemplo a seguir cria uma NIC chamada `myNic1`:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic1 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
```

Crie a segunda NIC. O exemplo a seguir cria uma NIC chamada `myNic2`:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic2 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
```

Verifique as duas NICs usando o analisador JSON:

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
azure network nic show myResourceGroup myNic2 --json | jq '.'
```

Crie o grupo de segurança de rede. O exemplo a seguir cria um grupo de segurança de rede chamado `myNetworkSecurityGroup`:

```azurecli
azure network nsg create -g myResourceGroup -l westeurope \
  -n myNetworkSecurityGroup
```

Adicione duas regras de entrada para o grupo de segurança de rede. O exemplo a seguir cria duas regras, `myNetworkSecurityGroupRuleSSH` e `myNetworkSecurityGroupRuleHTTP`:

```azurecli
azure network nsg rule create -p tcp -r inbound -y 1000 -u 22 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleSSH
azure network nsg rule create -p tcp -r inbound -y 1001 -u 80 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleHTTP
```

Verifique o grupo de segurança de rede e as regras de entrada usando o analisador JSON:

```azurecli
azure network nsg show -g myResourceGroup -n myNetworkSecurityGroup --json | jq '.'
```

Associe o grupo de segurança de rede às duas NICs:

```azurecli
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic1
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic2
```

Crie o conjunto de disponibilidade. O exemplo a seguir cria um conjunto de disponibilidade chamado `myAvailabilitySet`:

```azurecli
azure availset create -g myResourceGroup -l westeurope -n myAvailabilitySet
```

Crie a primeira VM do Linux. O exemplo a seguir cria uma VM chamada `myVM1`:

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

Crie a segunda VM do Linux. O exemplo a seguir cria uma VM chamada `myVM2`:

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

Use o analisador JSON para verificar se tudo o que foi criado:

```azurecli
azure vm show -g myResourceGroup -n myVM1 --json | jq '.'
azure vm show -g myResourceGroup -n myVM2 --json | jq '.'
```

Exporte seu novo ambiente para um modelo para recriar rapidamente novas instâncias:

```azurecli
azure group export myResourceGroup
```

## <a name="detailed-walkthrough"></a>Instruções detalhadas
As etapas detalhadas a seguir explicam o que cada comando está fazendo ao criar seu ambiente. Esses conceitos são úteis quando você cria seus próprios ambientes personalizados para desenvolvimento ou produção.

Verifique se você tem [a CLI clássica do Azure](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) conectada e usando o modo do Resource Manager:

```azurecli
azure config mode arm
```

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo pelos seus próprios valores. Os nomes de parâmetro de exemplo incluem `myResourceGroup`, `mystorageaccount`e `myVM`.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Criar grupos de recursos e escolher locais de implantação
Os grupos de recursos do Azure são entidades de implantação lógica que contêm informações de configuração e metadados para habilitar o gerenciamento lógico de implantações de recursos. O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na localização `westeurope`:

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
Você precisa de contas de armazenamento para seus discos de VM e para quaisquer discos de dados adicionais que deseja adicionar. Você cria contas de armazenamento quase imediatamente depois de criar grupos de recursos.

Aqui, usamos o comando `azure storage account create`, passando o local da conta, o grupo de recursos que o controla e o tipo de suporte de armazenamento desejado. O exemplo a seguir cria uma conta de armazenamento chamada `mystorageaccount`:

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

Para examinar nosso grupo de recursos usando o comando `azure group show`, vamos usar a ferramenta [JQ](https://stedolan.github.io/jq/) juntamente com a opção `--json` CLI do Azure. (Você pode usar **jsawk** ou qualquer biblioteca de idiomas que preferir para analisar o JSON.)

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

Para investigar a conta de armazenamento usando a CLI, primeiro você precisa definir os nomes e as chaves da conta. Substitua o nome da conta de armazenamento no exemplo a seguir por um nome que você escolher:

```bash
export AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show mystorageaccount --resource-group myResourceGroup --json | jq -r '.string')"
```

Em seguida, você pode exibir suas informações de armazenamento facilmente:

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
Em seguida, será necessário criar uma rede virtual em execução no Azure e uma sub-rede na qual você possa criar suas VMs. O exemplo a seguir cria uma rede virtual chamada `myVnet` com o prefixo de endereço `192.168.0.0/16`:

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

Novamente, vamos usar a opção--JSON de `azure group show` e `jq` para ver como estamos criando nossos recursos. Agora temos um recurso de `storageAccounts` e um recurso de `virtualNetworks`.  

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

Agora, vamos criar uma sub-rede na rede virtual `myVnet` na qual as VMs são implantadas. Usamos o comando `azure network vnet subnet create`, juntamente com os recursos que já criamos: o grupo de recursos `myResourceGroup` e a rede virtual `myVnet`. No exemplo a seguir, adicionamos a sub-rede chamada `mySubnet` com o prefixo de endereço de sub-rede `192.168.1.0/24`:

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

Como a sub-rede está logicamente dentro da rede virtual, procuramos as informações de sub-rede com um comando ligeiramente diferente. O comando que usamos é `azure network vnet show`, mas continuamos examinando a saída JSON usando `jq`.

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
Agora, vamos criar o endereço IP público (PIP) que atribuímos ao balanceador de carga. Ele permite que você se conecte às suas VMs da Internet usando o comando `azure network public-ip create`. Como o endereço padrão é dinâmico, criamos uma entrada DNS nomeada no domínio **cloudapp.Azure.com** usando a opção `--domain-name-label`. O exemplo a seguir cria um IP público chamado `myPublicIP` com o nome DNS de `mypublicdns`. Como o nome DNS deve ser exclusivo, você fornece seu próprio nome DNS exclusivo:

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

O endereço IP público também é um recurso de nível superior, para que você possa vê-lo com `azure group show`.

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

Você pode investigar mais detalhes do recurso, incluindo o FQDN (nome de domínio totalmente qualificado) do subdomínio, usando o comando `azure network public-ip show` completo. O recurso de endereço IP público foi alocado logicamente, mas um endereço específico ainda não foi atribuído. Para obter um endereço IP, você precisará de um balanceador de carga, que ainda não criamos.

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

## <a name="create-a-load-balancer-and-ip-pools"></a>Criar um balanceador de carga e pools de IPS
Quando você cria um balanceador de carga, ele permite que você distribua o tráfego entre várias VMs. Ele também fornece redundância para seu aplicativo executando várias VMs que respondem às solicitações do usuário em caso de manutenção ou cargas pesadas. O exemplo a seguir cria um balanceador de carga chamado `myLoadBalancer`:

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

Nosso balanceador de carga está razoavelmente vazio, então vamos criar alguns pools de IPS. Queremos criar dois pools de IPS para nosso balanceador de carga, um para o front-end e outro para o back-end. O pool de IPS de front-end é visível publicamente. Também é o local para o qual atribuímos o PIP que criamos anteriormente. Em seguida, usamos o pool de back-ends como um local para que as nossas VMs se conectem. Dessa forma, o tráfego pode fluir pelo balanceador de carga para as VMs.

Primeiro, vamos criar nosso pool de IPS de front-end. O exemplo a seguir cria um pool de front-end chamado `myFrontEndPool`:

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

Observe como usamos a opção `--public-ip-name` para passar o `myPublicIP` que criamos anteriormente. A atribuição do endereço IP público ao balanceador de carga permite que você alcance suas VMs pela Internet.

Em seguida, vamos criar nosso segundo pool de IPS, desta vez para nosso tráfego de back-end. O exemplo a seguir cria um pool de back-end chamado `myBackEndPool`:

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

Podemos ver como nosso balanceador de carga está fazendo examinando `azure network lb show` e examinando a saída JSON:

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

## <a name="create-load-balancer-nat-rules"></a>Criar regras NAT do balanceador de carga
Para obter o tráfego que flui pelo balanceador de carga, precisamos criar regras de NAT (conversão de endereços de rede) que especifiquem ações de entrada ou de saída. Você pode especificar o protocolo a ser usado e mapear portas externas para portas internas, conforme desejado. Para nosso ambiente, vamos criar algumas regras que permitem o SSH por meio de nosso balanceador de carga para nossas VMs. Configuramos as portas TCP 4222 e 4223 para direcionar para a porta TCP 22 em nossas VMs (que criamos mais tarde). O exemplo a seguir cria uma regra chamada `myLoadBalancerRuleSSH1` para mapear a porta TCP 4222 para a porta 22:

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

Repita o procedimento para a segunda regra NAT para SSH. O exemplo a seguir cria uma regra chamada `myLoadBalancerRuleSSH2` para mapear a porta TCP 4223 para a porta 22:

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22
```

Vamos também continuar e criar uma regra NAT para a porta TCP 80 para o tráfego da Web, vinculando a regra a nossos pools de IPS. Se vincularmos a regra a um pool de IPS, em vez de conectar a regra às nossas VMs individualmente, poderemos adicionar ou remover VMs do pool de IPS. O balanceador de carga ajusta automaticamente o fluxo de tráfego. O exemplo a seguir cria uma regra chamada `myLoadBalancerRuleWeb` para mapear a porta TCP 80 para a porta 80:

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
Uma investigação de integridade verifica periodicamente as VMs que estão atrás de nosso balanceador de carga para verificar se estão operando e respondendo às solicitações conforme definido. Caso contrário, eles serão removidos da operação para garantir que os usuários não estejam sendo direcionados a eles. Você pode definir verificações personalizadas para a investigação de integridade, juntamente com intervalos e valores de tempo limite. Para obter mais informações sobre investigações de integridade, consulte [Load Balancer investigações](../../load-balancer/load-balancer-custom-probe-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). O exemplo a seguir cria uma investigação de integridade TCP chamada `myHealthProbe`:

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

Aqui, especificamos um intervalo de 15 segundos para nossas verificações de integridade. Podemos perder um máximo de quatro investigações (um minuto) antes que o balanceador de carga considere que o host não está mais funcionando.

## <a name="verify-the-load-balancer"></a>Verificar o balanceador de carga
Agora, a configuração do balanceador de carga é feita. Aqui estão as etapas que você levou:

1. Você criou um balanceador de carga.
2. Você criou um pool de IPS de front-end e atribuiu um IP público a ele.
3. Você criou um pool de IPS de back-end ao qual as VMs podem se conectar.
4. Você criou regras de NAT que permitem o SSH para as VMs para gerenciamento, juntamente com uma regra que permite a porta TCP 80 para nosso aplicativo Web.
5. Você adicionou uma investigação de integridade para verificar periodicamente as VMs. Essa investigação de integridade garante que os usuários não tentem acessar uma VM que não está mais funcionando ou atendendo conteúdo.

Vamos examinar o que o balanceador de carga tem como agora:

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

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Criar uma NIC para usar com a VM do Linux
As NICs estão programaticamente disponíveis porque você pode aplicar regras ao seu uso. Você também pode ter mais de um. No comando `azure network nic create` a seguir, você conecta a NIC ao pool de IPS de back-end de carga e a associa à regra NAT para permitir o tráfego SSH.

Substitua as seções `#####-###-###` pela sua própria ID de assinatura do Azure. Sua ID de assinatura é observada na saída de `jq` quando você examina os recursos que está criando. Você também pode exibir sua ID de assinatura com `azure account list`.

O exemplo a seguir cria uma NIC chamada `myNic1`:

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

Você pode ver os detalhes examinando o recurso diretamente. Examine o recurso usando o comando `azure network nic show`:

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

Agora, criamos a segunda NIC, conectando-se ao nosso pool de IPS de back-end novamente. Desta vez, a segunda regra NAT permite o tráfego SSH. O exemplo a seguir cria uma NIC chamada `myNic2`:

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 \
  --lb-address-pool-ids  /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2
```

## <a name="create-a-network-security-group-and-rules"></a>Criar um grupo de segurança de rede e regras
Agora, criamos um grupo de segurança de rede e as regras de entrada que regem o acesso à NIC. Um grupo de segurança de rede pode ser aplicado a uma NIC ou sub-rede. Você define regras para controlar o fluxo de tráfego de entrada e saída de suas VMs. O exemplo a seguir cria um grupo de segurança de rede chamado `myNetworkSecurityGroup`:

```azurecli
azure network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Vamos adicionar a regra de entrada para o NSG para permitir conexões de entrada na porta 22 (para dar suporte ao SSH). O exemplo a seguir cria uma regra chamada `myNetworkSecurityGroupRuleSSH` para permitir TCP na porta 22:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1000 --destination-port-range 22 --access allow \
  --name myNetworkSecurityGroupRuleSSH
```

Agora, vamos adicionar a regra de entrada para o NSG para permitir conexões de entrada na porta 80 (para dar suporte ao tráfego da Web). O exemplo a seguir cria uma regra chamada `myNetworkSecurityGroupRuleHTTP` para permitir TCP na porta 80:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1001 --destination-port-range 80 --access allow \
  --name myNetworkSecurityGroupRuleHTTP
```

> [!NOTE]
> A regra de entrada é um filtro para conexões de rede de entrada. Neste exemplo, associamos o NSG à NIC virtual de VMs, o que significa que qualquer solicitação para a porta 22 é passada para a NIC em nossa VM. Essa regra de entrada é sobre uma conexão de rede, e não sobre um ponto de extremidade, que é o que seria sobre as implantações clássicas. Para abrir uma porta, você deve deixar o `--source-port-range` definido como '\*' (o valor padrão) para aceitar solicitações de entrada de **qualquer** porta solicitante. As portas normalmente são dinâmicas.
>
>

## <a name="bind-to-the-nic"></a>Associar à NIC
Associe o NSG às NICs. Precisamos conectar nossas NICs ao nosso grupo de segurança de rede. Execute os dois comandos para conectar ambas as NICs:

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic1 \
  --network-security-group-name myNetworkSecurityGroup
```

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic2 \
  --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade
Os conjuntos de disponibilidade ajudam a distribuir suas VMs entre domínios de falha e domínios de atualização. Vamos criar um conjunto de disponibilidade para suas VMs. O exemplo a seguir cria um conjunto de disponibilidade chamado `myAvailabilitySet`:

```azurecli
azure availset create --resource-group myResourceGroup --location westeurope
  --name myAvailabilitySet
```

Os domínios de falha definem um agrupamento de máquinas virtuais que compartilham uma fonte de energia e um comutador de rede comuns. Por padrão, as máquinas virtuais configuradas em seu conjunto de disponibilidade são separadas em até três domínios de falha. A ideia é que um problema de hardware em um desses domínios de falha não afeta cada VM que está executando seu aplicativo. O Azure distribui automaticamente as VMs entre os domínios de falha ao colocá-las em um conjunto de disponibilidade.

Os domínios de atualização indicam grupos de máquinas virtuais e hardware físico subjacente que podem ser reinicializados ao mesmo tempo. A ordem na qual os domínios de atualização são reinicializados pode não ser sequencial durante a manutenção planejada, mas apenas uma atualização é reinicializada por vez. Novamente, o Azure distribui automaticamente suas VMs entre domínios de atualização ao colocá-las em um site de disponibilidade.

Leia mais sobre como [gerenciar a disponibilidade de VMs](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="create-the-linux-vms"></a>Criar as VMs do Linux
Você criou os recursos de armazenamento e rede para dar suporte a VMs acessíveis pela Internet. Agora, vamos criar essas VMs e protegê-las com uma chave SSH que não tem uma senha. Nesse caso, vamos criar uma VM do Ubuntu com base no LTS mais recente. Localizamos essas informações de imagem usando `azure vm image list`, conforme descrito em [localizando imagens de VM do Azure](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Selecionamos uma imagem usando o comando `azure vm image list westeurope canonical | grep LTS`. Nesse caso, usamos `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`. Para o último campo, passamos `latest` de modo que, no futuro, sempre obteremos o Build mais recente. (A cadeia de caracteres que usamos é `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`).

Esta próxima etapa é familiar a qualquer pessoa que já tenha criado um par de chaves pública e privada do ssh RSA no Linux ou Mac usando **ssh-keygen-t RSA-b 2048**. Se você não tiver pares de chaves de certificado em seu diretório `~/.ssh`, poderá criá-los:

* Automaticamente, usando a opção `azure vm create --generate-ssh-keys`.
* Manualmente, usando [as instruções para criá-las por conta própria](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Como alternativa, você pode usar o método `--admin-password` para autenticar suas conexões SSH após a criação da VM. Normalmente, esse método é menos seguro.

Criamos a VM reunindo todos os nossos recursos e informações com o comando `azure vm create`:

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

Você pode se conectar à sua VM imediatamente usando suas chaves SSH padrão. Certifique-se de especificar a porta apropriada, já que estamos passando pelo balanceador de carga. (Para nossa primeira VM, configuramos a regra NAT para encaminhar a porta 4222 para nossa VM.)

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

Vá em frente e crie sua segunda VM da mesma maneira:

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

E agora você pode usar o comando `azure vm show myResourceGroup myVM1` para examinar o que você criou. Neste ponto, você está executando suas VMs do Ubuntu por trás de um balanceador de carga no Azure que você pode entrar somente com o par de chaves SSH (porque as senhas estão desabilitadas). Você pode instalar o Nginx ou o http, implantar um aplicativo Web e ver o fluxo de tráfego por meio do balanceador de carga para ambas as VMs.

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
Agora que você criou esse ambiente, e se quiser criar um ambiente de desenvolvimento adicional com os mesmos parâmetros ou um ambiente de produção que corresponda a ele? O Gerenciador de recursos usa modelos JSON que definem todos os parâmetros para o seu ambiente. Você cria ambientes inteiros referenciando esse modelo JSON. Você pode [criar modelos JSON manualmente](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou exportar um ambiente existente para criar o modelo JSON para você:

```azurecli
azure group export --name myResourceGroup
```

Esse comando cria o arquivo de `myResourceGroup.json` no diretório de trabalho atual. Ao criar um ambiente a partir desse modelo, você será solicitado a fornecer todos os nomes de recursos, incluindo os nomes do balanceador de carga, interfaces de rede ou VMs. Você pode preencher esses nomes no arquivo de modelo adicionando o parâmetro `-p` ou `--includeParameterDefaultValue` ao comando `azure group export` que foi mostrado anteriormente. Edite o modelo JSON para especificar os nomes dos recursos ou [crie um arquivo Parameters. JSON](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) que especifique os nomes dos recursos.

Para criar um ambiente a partir do seu modelo:

```azurecli
azure group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

Talvez você queira ler [mais sobre como implantar a partir de modelos](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Saiba mais sobre como atualizar ambientes de forma incremental, use o arquivo de parâmetros e acesse modelos de um único local de armazenamento.

## <a name="next-steps"></a>Passos seguintes
Agora você está pronto para começar a trabalhar com vários componentes de rede e VMs. Você pode usar este ambiente de exemplo para criar seu aplicativo usando os componentes principais introduzidos aqui.
