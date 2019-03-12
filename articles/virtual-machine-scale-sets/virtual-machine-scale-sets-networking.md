---
title: Trabalhar em rede em conjuntos de dimensionamento de máquinas virtuais do Azure | Microsoft Docs
description: Propriedades de rede de configuração para conjuntos de dimensionamento de máquinas virtuais do Azure.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/17/2017
ms.author: manayar
ms.openlocfilehash: 656645326314fec42ba909957c9dc27fe88ae338
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57773304"
---
# <a name="networking-for-azure-virtual-machine-scale-sets"></a>Trabalhar em rede em conjuntos de dimensionamento de máquinas virtuais do Azure

Quando implementa um conjunto de dimensionamento de máquinas virtuais do Azure através do portal, determinadas propriedades de rede são predefinidas, como, por exemplo, um Balanceador de Carga do Azure com regras NAT de entrada. Este artigo descreve como utilizar algumas das funcionalidades de rede mais avançadas que podem ser configuradas em conjuntos de dimensionamento.

Pode configurar todas as funcionalidades abordadas neste artigo através de modelos do Azure Resource Manager. Também estão incluídos exemplos da CLI do Azure e do PowerShell para determinadas funcionalidades.

## <a name="accelerated-networking"></a>Redes Aceleradas
As Redes Aceleradas do Azure melhoram o desempenho da rede ao permitirem a virtualização de E/S de raiz única (SR-IOV) para uma máquina virtual. Para saber mais sobre a utilização das Redes aceleradas, consulte Redes aceleradas para máquinas virtuais [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md) ou [Linux](../virtual-network/create-vm-accelerated-networking-cli.md). Para utilizar as redes aceleradas com conjuntos de dimensionamento, defina enableAcceleratedNetworking como **Verdadeiro** nas definições networkInterfaceConfigurations do seu conjunto. Por exemplo:
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
    {
      "name": "niconfig1",
      "properties": {
        "primary": true,
        "enableAcceleratedNetworking" : true,
        "ipConfigurations": [
          ...
        ]
      }
    }
   ]
}
```

## <a name="create-a-scale-set-that-references-an-existing-azure-load-balancer"></a>Criar um conjunto de dimensionamento que faça referência a um balanceador de carga do Azure existente
Quando um conjunto de dimensionamento é criado no portal do Azure, é criado um novo balanceador de carga para a maioria das opções de configuração. Se criar um conjunto de dimensionamento que tenha de referenciar um balanceador de carta existente, poderá utilizar a CLI para o fazer. O script de exemplo seguinte cria um balanceador de carta e, depois, um conjunto de dimensionamento, que o referencia:
```bash
az network lb create \
    -g lbtest \
    -n mylb \
    --vnet-name myvnet \
    --subnet mysubnet \
    --public-ip-address-allocation Static \
    --backend-pool-name mybackendpool

az vmss create \
    -g lbtest \
    -n myvmss \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --admin-username negat \
    --ssh-key-value /home/myuser/.ssh/id_rsa.pub \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name myvnet \
    --subnet mysubnet \
    --lb mylb \
    --backend-pool-name mybackendpool
```

## <a name="create-a-scale-set-that-references-an-application-gateway"></a>Criar um conjunto de dimensionamento que referencie um Gateway de Aplicação
Para criar um conjunto de dimensionamento que utiliza um gateway de aplicação, referencie o conjunto de endereços de back-end do gateway de aplicação na secção ipConfigurations do seu conjunto de dimensionamento como nesta configuração de modelo ARM:
```json
"ipConfigurations": [{
  "name": "{config-name}",
  "properties": {
  "subnet": {
    "id": "{subnet-id}"
  },
  "ApplicationGatewayBackendAddressPools": [{
    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/applicationGateways/{gateway-name}/backendAddressPools/{pool-name}"
  }]
}]
```

>[!NOTE]
> Tenha em atenção que o gateway de aplicação tem de estar na mesma rede virtual do conjunto de dimensionamento, mas tem de estar numa sub-rede diferente do conjunto de dimensionamento.


## <a name="configurable-dns-settings"></a>Definições de DNS Configuráveis
Por predefinição, os conjuntos de dimensionamento assumem as definições de DNS específicas da VNet e da sub-rede em que foram criados. Contudo, pode configurar as definições de DNS de um conjunto de dimensionamento diretamente.

### <a name="creating-a-scale-set-with-configurable-dns-servers"></a>Criar um conjunto de dimensionamento com servidores DNS configuráveis
Para criar um conjunto de dimensionamento com uma configuração DNS personalizada através da CLI do Azure, adicione o argumento **--dns-servers** ao comando **vmss create**, seguido pelos endereços IP dos servidores separados por espaços. Por exemplo:
```bash
--dns-servers 10.0.0.6 10.0.0.5
```
Para configurar servidores DNS personalizados num modelo do Azure, adicione uma propriedade dnsSettings à secção networkInterfaceConfigurations do conjunto de dimensionamento. Por exemplo:
```json
"dnsSettings":{
    "dnsServers":["10.0.0.6", "10.0.0.5"]
}
```

### <a name="creating-a-scale-set-with-configurable-virtual-machine-domain-names"></a>Criar um conjunto de dimensionamento com nomes de domínio de máquinas virtuais configuráveis
Para criar um conjunto de dimensionamento com um nome DNS personalizado para as máquinas virtuais através da CLI, adicione o argumento **--vm-domain-name** ao comando **virtual machine scale set create**, seguido por uma cadeia de carateres que representa o nome de domínio.

Para definir o nome de domínio num modelo do Azure, adicione uma propriedade **dnsSettings** à secção **networkInterfaceConfigurations** do conjunto de dimensionamento. Por exemplo:

```json
"networkProfile": {
  "networkInterfaceConfigurations": [
    {
    "name": "nic1",
    "properties": {
      "primary": "true",
      "ipConfigurations": [
      {
        "name": "ip1",
        "properties": {
          "subnet": {
            "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
          },
          "publicIPAddressconfiguration": {
            "name": "publicip",
            "properties": {
            "idleTimeoutInMinutes": 10,
              "dnsSettings": {
                "domainNameLabel": "[parameters('vmssDnsName')]"
              }
            }
          }
        }
      }
    ]
    }
}
```

O resultado para um nome dns de máquina virtual individual teria o formato seguinte: 
```
<vm><vmindex>.<specifiedVmssDomainNameLabel>
```

## <a name="public-ipv4-per-virtual-machine"></a>IPv4 Público por máquina virtual
Em geral, as máquinas virtuais de conjuntos de dimensionamento do Azure não precisam de ter os seus próprios endereços IP públicos. Na maioria dos cenários, é mais económico e seguro associar um endereço IP público a um balanceador de carga ou a uma máquina virtual individual (também chamada “jumpbox”), que depois encamihna as ligações de entrada às máquinas virtuais do conjunto de dimensionamento, conforme necessário (por exemplo, através de regras NAT de entrada).

No entanto, alguns cenários requerem que as máquinas virtuais do conjunto de dimensionamento tenham os seus próprios endereços IP públicos. Um exemplo de um cenário destes são os jogos, em que uma consola tem de fazer uma ligação direta a uma máquina virtual na cloud, que está a fazer o processamento físico do jogo. Outro exemplo é quando as máquinas virtuais têm de fazer ligações externas a outras em várias regiões numa base de dados distribuída.

### <a name="creating-a-scale-set-with-public-ip-per-virtual-machine"></a>Criar um conjunto de dimensionamento com IP público por máquina virtual
Para criar um conjunto de dimensionamento que atribui um endereço IP público a cada máquina virtual através da CLI, adicione o parâmetro **--public-ip-per-vm** ao comando **vmss create**. 

Para criar um conjunto de dimensionamento com um modelo do Azure, confirme que a versão da API do recurso Microsoft.Compute/virtualMachineScaleSets é, no mínimo, a **2017-03-30** e adicione a propriedade JSON **publicIpAddressConfiguration** à secção ipConfigurations do conjunto. Por exemplo:

```json
"publicIpAddressConfiguration": {
    "name": "pub1",
    "properties": {
      "idleTimeoutInMinutes": 15
    }
}
```
Modelo de exemplo: [201-vmss-public-ip-linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-public-ip-linux)

### <a name="querying-the-public-ip-addresses-of-the-virtual-machines-in-a-scale-set"></a>Consultar os endereços IP públicos das máquinas virtuais num conjunto de dimensionamento
Para listar os endereços IP públicos atribuídos às máquinas virtuais do conjunto de dimensionamento através da CLI, utilize o comando **az vmss list-instance-public-ips**.

Para listar o IP público de conjunto de dimensionamento de endereços com o PowerShell, utilize o _Get-AzPublicIpAddress_ comando. Por exemplo:
```PowerShell
Get-AzPublicIpAddress -ResourceGroupName myrg -VirtualMachineScaleSetName myvmss
```

Também pode consultar os endereços IP públicos ao referenciar diretamente o ID de recurso da configuração desses endereços. Por exemplo:
```PowerShell
Get-AzPublicIpAddress -ResourceGroupName myrg -Name myvmsspip
```

Também pode apresentar os endereços IP públicos atribuídos às máquinas virtuais do conjunto de dimensionamento ao consultar o [Azure Resource Explorer](https://resources.azure.com) ou a API REST do Azure com a versão **2017-03-30** ou superior.

Para consultar o [Azure Resource Explorer](https://resources.azure.com):

1. Abra o [Azure Resource Explorer](https://resources.azure.com) num browser da Web.
1. Expanda *subscrições* no lado esquerdo ao clicar em *+* junto ao mesmo. Se tiver apenas um item sob *subscrições*, pode já estar expandido.
1. Expanda a sua subscrição.
1. Expanda o seu grupo de recursos.
1. Expanda *fornecedores*.
1. Expanda *Microsoft.Compute*.
1. Expanda *virtualMachineScaleSets*.
1. Expanda o seu conjunto de dimensionamento.
1. Clique em *publicipaddresses*.

Para consultar a API REST do Azure:

```bash
GET https://management.azure.com/subscriptions/{your sub ID}/resourceGroups/{RG name}/providers/Microsoft.Compute/virtualMachineScaleSets/{scale set name}/publicipaddresses?api-version=2017-03-30
```

Exemplo de saída do [Azure Resource Explorer](https://resources.azure.com) e a API REST do Azure:
```json
{
  "value": [
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/pipvmss/virtualMachines/0/networkInterfaces/pipvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"a64060d5-4dea-4379-a11d-b23cd49a3c8d\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "ee8cb20f-af8e-4cd6-892f-441ae2bf701f",
        "ipAddress": "13.84.190.11",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/0/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    },
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"5f6ff30c-a24c-4818-883c-61ebd5f9eee8\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "036ce266-403f-41bd-8578-d446d7397c2f",
        "ipAddress": "13.84.159.176",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    }
```

## <a name="multiple-ip-addresses-per-nic"></a>Vários endereços IP por NIC
Todas as NICs ligadas a uma VM dentro de um conjunto de dimensionamento podem ter uma ou mais configurações de IP associadas a si. É atribuído um endereço IP privado a cada configuração. Cada configuração também pode ter um recurso de endereço IP público associado a si. Para compreender quantos endereços IP podem ser atribuídos a uma NIC e quantos pode utilizar numa subscrição do Azure, veja os [limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="multiple-nics-per-virtual-machine"></a>Várias NICs por máquina virtual
Pode ter até 8 NICs por máquina virtual, dependendo do tamanho da máquina. O número máximo de NICs por máquina está disponível no [artigo relativo aos tamanhos das VMs](../virtual-machines/windows/sizes.md). Todas as NICs ligadas a uma instância de VM têm de estar ligadas à mesma rede virtual. As NICs podem ligar-se a sub-redes diferentes, mas todas as sub-redes têm de fazer parte da mesma rede virtual.

O exemplo seguinte é um perfil de rede de um conjunto de dimensionamento que mostra várias entradas de NICs e múltiplos IPs públicos por máquina virtual:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
        "name": "nic1",
        "properties": {
            "primary": "true",
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        },
        {
        "name": "nic2",
        "properties": {
            "primary": "false",
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        }
    ]
}
```

## <a name="nsg--asgs-per-scale-set"></a>NSG e ASGs por conjunto de dimensionamento
Os [Grupos de Segurança de Rede](../virtual-network/security-overview.md) permitem-lhe filtrar o tráfego de e para os recursos do Azure numa rede virtual do Azure com regras de segurança. Os [Grupos de Segurança de Aplicação](../virtual-network/security-overview.md#application-security-groups) permitem-lhe lidar com a segurança de rede de recursos do Azure e agrupá-los como uma extensão da estrutura da sua aplicação.

Podem ser aplicados Grupos de Segurança de Rede diretamente a um conjunto de dimensionamento. Para tal, adicione uma referência à secção de configuração da interface de rede das propriedades da máquina virtual do conjunto.

Os Grupos de Segurança de Aplicação podem ser diretamente especificados para um conjunto de dimensionamento. Para tal, adicione uma referência à secção de configuração do IP da interface de rede das propriedades da máquina virtual do conjunto de dimensionamento.

Por exemplo: 
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": "true",
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            },
                            "applicationSecurityGroups": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/applicationSecurityGroups/', variables('asgName'))]"
                                }
                            ],
                "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

Para verificar se o seu Grupo de Segurança de Rede está associado ao seu conjunto de dimensionamento, utilize o comando `az vmss show`. O exemplo abaixo utiliza `--query` para filtrar os resultados e mostrar apenas a secção relevante da saída.

```bash
az vmss show \
    -g myResourceGroup \
    -n myScaleSet \
    --query virtualMachineProfile.networkProfile.networkInterfaceConfigurations[].networkSecurityGroup

[
  {
    "id": "/subscriptions/.../resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/nsgName",
    "resourceGroup": "myResourceGroup"
  }
]
```

Para verificar se o seu Grupo de Segurança da Aplicação está associado ao seu conjunto de dimensionamento, utilize o comando `az vmss show`. O exemplo abaixo utiliza `--query` para filtrar os resultados e mostrar apenas a secção relevante da saída.

```bash
az vmss show \
    -g myResourceGroup \
    -n myScaleSet \
    --query virtualMachineProfile.networkProfile.networkInterfaceConfigurations[].ipConfigurations[].applicationSecurityGroups

[
  [
    {
      "id": "/subscriptions/.../resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationSecurityGroups/asgName",
      "resourceGroup": "myResourceGroup"
    }
  ]
]
```



## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre redes virtuais do Azure, veja [Descrição geral das redes virtuais do Azure](../virtual-network/virtual-networks-overview.md).
