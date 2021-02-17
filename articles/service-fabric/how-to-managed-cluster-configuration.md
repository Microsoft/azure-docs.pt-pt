---
title: Configure o cluster gerido do seu Tecido de Serviço (pré-visualização)
description: Saiba como configurar o seu cluster gerido pelo Service Fabric para atualizações automáticas de SO, regras NSG e muito mais.
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: 90ba5057f06cf8841e278b8d921d812286459c49
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100642498"
---
# <a name="service-fabric-managed-cluster-preview-configuration-options"></a>Opções de configuração geridas por cluster de tecido de serviço (pré-visualização)

Além de selecionar o [cluster gerido pelo Service Fabric SKU](overview-managed-cluster.md#service-fabric-managed-cluster-skus) ao criar o seu cluster, existem várias outras formas de o configurar. Na pré-visualização atual, pode:

* Adicione uma [extensão de conjunto de escala de máquina virtual](how-to-managed-cluster-vmss-extension.md) a um tipo de nó
* Ativar [upgrades automáticos de SO](how-to-managed-cluster-configuration.md#enable-automatic-os-image-upgrades) para os seus nos seus nosmos
* Ativar [a encriptação do sistema operativo e do disco de dados](how-to-enable-managed-cluster-disk-encryption.md) nos seus nos seus
* Sobre [configurações de networking](how-to-managed-cluster-configuration.md#networking-configurations)
* Configurar [identidade gerida](how-to-managed-identity-managed-cluster-virtual-machine-scale-sets.md) nos seus tipos de nó

## <a name="networking-configurations"></a>Configurações de rede

Os clusters geridos por tecido de serviço são criados com uma configuração de rede padrão. Esta configuração consiste em regras obrigatórias para a funcionalidade essencial do cluster, e algumas regras opcionais que se destinam a facilitar a configuração do cliente.

Além da configuração de rede padrão, pode modificar as regras de networking para atender às necessidades do seu cenário. 

### <a name="nsg-rules-guidance"></a>Orientação das regras do NSG

* Os clusters geridos pelo Tecido de Serviço reservam a gama prioritária de regras NSG 0 a 999 para funcionalidades essenciais. Não é possível criar regras NSG personalizadas com um valor prioritário inferior a 1000. 
* Os clusters geridos pela Service Fabric reservam a gama prioritária 3001 a 4000 para a criação de regras opcionais de NSG. Estas regras são adicionadas automaticamente para tornar as configurações rápidas e fáceis. Pode anular estas regras adicionando regras NSG personalizadas na gama prioritária 1000 a 3000. 
* As regras NSG personalizadas devem ter uma prioridade dentro do intervalo de 1000 a 3000. 


### <a name="apply-nsg-rules"></a>Aplicar regras NSG

Com clusters clássicos (não geridos) de tecido de serviço, deve declarar e gerir um recurso separado *microsoft.Network/networkSecurityGroups* para [aplicar as regras do Grupo de Segurança de Rede (NSG) no seu cluster.](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-nsg-cluster-65-node-3-nodetype) Os clusters geridos por tecido de serviço permitem-lhe atribuir as regras NSG diretamente dentro do recurso de cluster do seu modelo de implementação.

Utilize a propriedade [networkSecurityRules](/azure/templates/microsoft.servicefabric/managedclusters#managedclusterproperties-object) do seu recurso *Microsoft.ServiceFabric/managedclusters* (versão `2021-01-01-preview` ou posterior) para atribuir regras NSG. Por exemplo:

```json
            "apiVersion": "2021-01-01-preview",
            "type": "Microsoft.ServiceFabric/managedclusters",
            ...
            "properties": {
                ...
                "networkSecurityRules" : [
                    {
                        "name": "AllowCustomers",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33000-33499",
                        "access": "Allow",
                        "priority": 2001,
                        "direction": "Inbound" 
                    },
                    {
                        "name": "AllowARM",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "AzureResourceManager",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33500-33699",
                        "access": "Allow",
                        "priority": 2002,
                        "direction": "Inbound" 
                    },
                    {
                        "name": "DenyCustomers",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33700-33799",
                        "access": "Deny",
                        "priority": 2003,
                        "direction": "Outbound"
                    },
                    {
                        "name": "DenyRDP",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "*",
                        "destinationAddressPrefix": "VirtualNetwork",
                        "destinationPortRange": "3389",
                        "access": "Deny",
                        "priority": 2004,
                        "direction": "Inbound",
                        "description": "Override for optional SFMC_AllowRdpPort rule. This is required in tests to avoid Sev2 incident for security policy violation."
                    }
                ],
                "fabricSettings": [
                ...
                ]
            }
```

### <a name="rdp-ports"></a>Portos RDP 

Os clusters geridos por tecido de serviço não permitem o acesso às portas RDP por padrão. Pode abrir portas RDP para a internet definindo a seguinte propriedade num recurso de cluster gerido por Service Fabric. 

```json
"allowRDPAccess": true 
```

Quando a propriedade allowRDPAccess for definida como verdadeira, a seguinte regra NSG será adicionada à sua implementação de cluster.  

```json
{
    "name": "SFMC_AllowRdpPort", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open RDP ports.",
        "protocol": "tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3002,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRange": "3389"
    }
}
```

### <a name="clientconnection-and-httpgatewayconnection-ports"></a>Opecion e portas HttpGatewayConnection 

**Regra NSG: SFMC_AllowServiceFabricGatewayToSFRP** Uma regra NSG predefinida é adicionada para permitir que o fornecedor de recursos de Service Fabric aceda ao cliente do clusterConnectionPort e httpGatewayConnectionPort. Esta regra permite o acesso às portas através da etiqueta de serviço 'ServiceFabric'.

>[!NOTE]
>Esta regra é sempre adicionada e não pode ser ultrapassada. 

```json
{ 
    "name": "SFMC_AllowServiceFabricGatewayToSFRP", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules", 
    "properties": { 
        "description": "This is required rule to allow SFRP to connect to the cluster. This rule cannot be overridden.", 
        "protocol": "TCP", 
        "sourcePortRange": "*", 
        "sourceAddressPrefix": "ServiceFabric", 
        "destinationAddressPrefix": "VirtualNetwork", 
        "access": "Allow", 
        "priority": 500, 
        "direction": "Inbound", 
        "sourcePortRanges": [], 
        "destinationPortRanges": [ 
            "19000", 
            "19080" 
        ] 
    } 
}
```

**Regra NSG: SFMC_AllowServiceFabricGatewayPorts** Esta é uma regra opcional do NSG para permitir o acesso ao clienteConnectionPort e httpGatewayPort a partir da internet. Esta regra permite que os clientes acedam ao SFX, conectem-se ao cluster usando o PowerShell e utilizem pontos finais de API de cluster de tecido de serviço de fora do. 

>[!NOTE]
>Esta regra não será adicionada se houver uma regra personalizada com os mesmos valores de acesso, direção e protocolo para a mesma porta. Pode anular esta regra com regras NSG personalizadas. 

```json
{ 
    "name": "SFMC_AllowServiceFabricGatewayPorts", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules", 
    "properties": { 
        "description": "Optional rule to open SF cluster gateway ports. To override add a custom NSG rule for gateway ports in priority range 1000-3000.", 
        "protocol": "tcp", 
        "sourcePortRange": "*", 
        "sourceAddressPrefix": "*", 
        "destinationAddressPrefix": "VirtualNetwork", 
        "access": "Allow", 
        "priority": 3001, 
        "direction": "Inbound", 
        "sourcePortRanges": [], 
        "destinationPortRanges": [ 
            "19000", 
            "19080" 
        ] 
    } 
}
```

### <a name="load-balancer-ports"></a>Portas de balançadores de carga 

Os clusters geridos pelo Tecido de Serviço criam uma regra NSG na gama de prioridades predefinidas para todas as portas LB configuradas na secção "loadBalancingRules" em propriedades ManagedCluster. Esta regra abre portas LB para tráfego de entrada a partir da internet.  

>[!NOTE]
>Esta regra é adicionada na gama de prioridades opcionais e pode ser ultrapassada adicionando regras NSG personalizadas. 

```json
{
    "name": "SFMC_AllowLoadBalancedPorts",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open LB ports",
        "protocol": "*", 
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3003,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
        "80", "8080", "4343"
        ]
    }
}
```

### <a name="load-balancer-probes"></a>Sondas de balançadores de carga

Os clusters geridos pelo Tecido de Serviço criam automaticamente sondas de balanceadores de carga para portas de gateway de tecido, bem como todas as portas configuradas sob a secção "loadBalancingRules" das propriedades geridas do cluster.

```json
{ 
  "value": [ 
    { 
        "name": "FabricTcpGateway", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 19000, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
                { 
                    "id": "<>"
                } 
            ] 
        }, 
        "type": "Microsoft.Network/loadBalancers/probes" 
    }, 
    { 
        "name": "FabricHttpGateway", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 19080, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
                { 
                    "id": "<>" 
                } 
            ]
        },
        "type": "Microsoft.Network/loadBalancers/probes" 
    },
    {
        "name": "probe1_tcp_8080", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 8080, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
            { 
                "id": "<>" 
            } 
        ] 
      }, 
      "type": "Microsoft.Network/loadBalancers/probes" 
    } 
  ] 
} 
```

## <a name="enable-automatic-os-image-upgrades"></a>Ativar upgrades automáticos de imagem de SO

Pode optar por ativar atualizações automáticas de imagens de SO para as máquinas virtuais que executam os seus nós de cluster geridos. Embora os recursos de escala de máquina virtual sejam geridos em seu nome com clusters geridos por Service Fabric, a escolha é ativar upgrades automáticos de imagem de SO para os seus nós de cluster. Tal como acontece com os agrupamentos [clássicos de Tecido](service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration) de Serviço, os nós de cluster geridos não são atualizados por padrão, de modo a evitar perturbações não intencionais no seu cluster.

Para permitir atualizações automáticas de SO:

* Utilize a `2021-01-01-preview` versão (ou posterior) do *Microsoft.ServiceFabric/gerenciou os aglomerados* e os recursos *de Microsoft.ServiceFabric/managedclusters/nodetypes*
* Definir a propriedade do cluster `enableAutoOSUpgrade` para *verdadeiro*
* Desaprote a propriedade de recursos dos nodeTypes de cluster `vmImageVersion` para o *mais recente*

Por exemplo:

```json
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters",
      ...
      "properties": {
        ...
        "enableAutoOSUpgrade": true
      },
    },
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
       ...
      "properties": {
        ...
        "vmImageVersion": "latest",
        ...
      }
    }
}

```

Uma vez ativado, o Service Fabric começará a consultar e rastrear as versões de imagem do SO no cluster gerido. Se estiver disponível uma nova versão SO, os tipos de nó de cluster (conjuntos de escala de máquina virtual) serão atualizados, um de cada vez. As atualizações de tempo de execução do tecido de serviço só são executadas depois de confirmar que não estão em curso atualizações de imagem do nó de cluster.

Se uma atualização falhar, o Service Fabric tentará novamente após 24 horas, para um máximo de três recauchutagens. Semelhantes às atualizações clássicas (não geridas) do Tecido de Serviço, aplicações ou nós pouco saudáveis podem bloquear a atualização da imagem do SO.

Para obter mais informações sobre atualizações de imagens, consulte [atualizações automáticas de imagens de SO com conjuntos de escala de máquina virtual Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md).

## <a name="next-steps"></a>Passos seguintes

[Link para modelo de amostra]

[Visão geral do cluster gerido]
