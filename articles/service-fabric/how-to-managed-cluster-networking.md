---
title: Configurar as definições de rede para clusters geridos por Tecido de Serviço (pré-visualização)
description: Saiba como configurar o cluster gerido do Seu Service Fabric para as regras NSG, acesso à porta RDP, regras de equilíbrio de carga e muito mais.
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: e17251523c0720665c4c6f5b7811304eebc9923e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746989"
---
# <a name="configure-network-settings-for-service-fabric-managed-clusters-preview"></a>Configurar as definições de rede para clusters geridos por Tecido de Serviço (pré-visualização)

Os clusters geridos por tecido de serviço são criados com uma configuração de rede padrão. Esta configuração consiste em regras obrigatórias para a funcionalidade essencial do cluster, e algumas regras opcionais que se destinam a facilitar a configuração do cliente.

Além da configuração de rede padrão, pode modificar as regras de networking para atender às necessidades do seu cenário.

## <a name="nsg-rules-guidance"></a>Orientação das regras do NSG

Esteja atento a estas considerações ao criar novas regras NSG para o seu cluster gerido.

* Os clusters geridos pelo Tecido de Serviço reservam a gama prioritária de regras NSG 0 a 999 para funcionalidades essenciais. Não é possível criar regras NSG personalizadas com um valor prioritário inferior a 1000.
* Os clusters geridos pela Service Fabric reservam a gama prioritária 3001 a 4000 para a criação de regras opcionais de NSG. Estas regras são adicionadas automaticamente para tornar as configurações rápidas e fáceis. Pode anular estas regras adicionando regras NSG personalizadas na gama prioritária 1000 a 3000.
* As regras NSG personalizadas devem ter uma prioridade dentro do intervalo de 1000 a 3000.

## <a name="apply-nsg-rules"></a>Aplicar regras NSG

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

## <a name="rdp-ports"></a>Portos RDP

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

## <a name="clientconnection-and-httpgatewayconnection-ports"></a>Opecion e portas HttpGatewayConnection

### <a name="nsg-rule-sfmc_allowservicefabricgatewaytosfrp"></a>Regra NSG: SFMC_AllowServiceFabricGatewayToSFRP

Uma regra NSG predefinida é adicionada para permitir que o fornecedor de recursos de Service Fabric aceda ao cliente do clusterConnectionPort e httpGatewayConnectionPort. Esta regra permite o acesso às portas através da etiqueta de serviço 'ServiceFabric'.

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

### <a name="nsg-rule-sfmc_allowservicefabricgatewayports"></a>Regra NSG: SFMC_AllowServiceFabricGatewayPorts

Esta é uma regra opcional do NSG para permitir o acesso ao clienteConnectionPort e httpGatewayPort a partir da internet. Esta regra permite que os clientes acedam ao SFX, conectem-se ao cluster usando o PowerShell e utilizem pontos finais de API de cluster de tecido de serviço de fora do. 

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

## <a name="load-balancer-ports"></a>Portas de balançadores de carga

Os clusters geridos pelo Tecido de Serviço criam uma regra NSG na gama de prioridades predefinidas para todas as portas do balançador de carga (LB) configuradas na secção "loadBalancingRules" nas propriedades *ManagedCluster.* Esta regra abre portas LB para tráfego de entrada a partir da internet.  

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

## <a name="load-balancer-probes"></a>Sondas de balançadores de carga

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

## <a name="next-steps"></a>Passos seguintes

[Opções de configuração de cluster geridos por tecido de serviço](how-to-managed-cluster-configuration.md)

[Visão geral dos clusters geridos de tecido de serviço](overview-managed-cluster.md)
