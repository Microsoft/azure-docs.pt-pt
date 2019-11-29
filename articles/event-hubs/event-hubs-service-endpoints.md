---
title: Pontos de extremidade de serviço de rede virtual-hubs de eventos do Azure | Microsoft Docs
description: Este artigo fornece informações sobre como adicionar um ponto de extremidade de serviço do Microsoft. EventHub a uma rede virtual.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 11/26/2019
ms.author: shvija
ms.openlocfilehash: 9b8b3600acc33e177e65002ba69dcf98a20c2253
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555334"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Usar pontos de extremidade de serviço de rede virtual com hubs de eventos do Azure

A integração dos hubs de eventos com [pontos de extremidade de serviço de rede virtual (VNet)][vnet-sep] permite o acesso seguro a recursos de mensagens de cargas de trabalho, como máquinas virtuais associadas a redes virtuais, com o caminho de tráfego de rede protegido em ambas as extremidades.

Uma vez configurado para ser associado a pelo menos um ponto de extremidade de serviço de sub-rede de rede virtual, o respectivo namespace de hubs de eventos não aceitará mais o tráfego de qualquer lugar, mas de sub-redes autorizadas em redes virtuais. Da perspectiva da rede virtual, a associação de um namespace de hubs de eventos a um ponto de extremidade de serviço configura um túnel de rede isolado da sub-rede da rede virtual para o serviço de mensagens. 

O resultado é uma relação privada e isolada entre as cargas de trabalho vinculadas à sub-rede e o respectivo namespace de hubs de eventos, apesar do endereço de rede observável do ponto de extremidade do serviço de mensagens estar em um intervalo de IP público. Há uma exceção a esse comportamento. Habilitar um ponto de extremidade de serviço, por padrão, habilita a regra denyall no firewall IP associado à rede virtual. Você pode adicionar endereços IP específicos no firewall de IP para habilitar o acesso ao ponto de extremidade público do hub de eventos. 


>[!WARNING]
> A implementação da integração de redes virtuais pode impedir que outros serviços do Azure interajam com os hubs de eventos.
>
> Os serviços confiáveis da Microsoft não têm suporte quando as redes virtuais são implementadas.
>
> Cenários comuns do Azure que não funcionam com redes virtuais (Observe que a lista **não** é exaustiva) –
> - Integração com o Azure Monitor. Não é possível transmitir logs de diagnóstico de **outros** serviços do Azure para os hubs de eventos. No entanto, você pode habilitar os logs de diagnóstico do Azure no próprio Hub de eventos. É o mesmo caso em que o firewall (filtragem de IP) está habilitado.
> - Azure Stream Analytics
> - Integração com a grade de eventos do Azure
> - Rotas do Hub IoT do Azure
> - Device Explorer de IoT do Azure
>
> Os serviços da Microsoft a seguir devem estar em uma rede virtual
> - Aplicações Web do Azure
> - Funções do Azure

> [!IMPORTANT]
> As redes virtuais têm suporte em camadas **padrão** e **dedicadas** dos hubs de eventos. Não há suporte na camada básica.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Cenários de segurança avançada habilitados pela integração de VNet 

Soluções que exigem segurança rígida e segmentada e onde as sub-redes da rede virtual fornecem a segmentação entre os serviços compartimentalizados, ainda precisam de caminhos de comunicação entre os serviços que residem nesses compartimentos.

Qualquer rota de IP imediata entre os compartimentos, incluindo aqueles que realizam HTTPS sobre TCP/IP, traz o risco de exploração de vulnerabilidades da camada de rede em funcionamento. Os serviços de mensagens fornecem caminhos de comunicação completamente isolados, em que as mensagens são gravadas no disco à medida que fazem a transição entre as partes. As cargas de trabalho em duas redes virtuais distintas associadas à mesma instância de hubs de eventos podem se comunicar de forma eficiente e confiável por meio de mensagens, enquanto a respectiva integridade de limite de isolamento de rede é preservada.
 
Isso significa que suas soluções de nuvem sensíveis à segurança não só têm acesso aos recursos de mensagens assíncronas confiáveis e escalonáveis líderes do setor do Azure, mas agora podem usar o sistema de mensagens para criar caminhos de comunicação entre compartimentos de solução seguros que são inerentemente mais seguras do que o que é atingível com qualquer modo de comunicação ponto a ponto, incluindo HTTPS e outros protocolos de soquete protegidos por TLS.

## <a name="bind-event-hubs-to-virtual-networks"></a>Associar hubs de eventos a redes virtuais

*As regras de rede virtual* são o recurso de segurança de firewall que controla se o namespace de hubs de eventos do Azure aceita conexões de uma sub-rede de rede virtual específica.

A associação de um namespace de hubs de eventos a uma rede virtual é um processo de duas etapas. Primeiro, você precisa criar um **ponto de extremidade de serviço de rede virtual** em uma sub-rede de rede virtual e habilitá-la para "Microsoft. EventHub", conforme explicado na [visão geral do ponto de extremidade do serviço][vnet-sep]. Depois de adicionar o ponto de extremidade de serviço, vincule o namespace de hubs de eventos a ele com uma *regra de rede virtual*.

A regra de rede virtual é uma associação do namespace de hubs de eventos com uma sub-rede de rede virtual. Embora a regra exista, todas as cargas de trabalho vinculadas à sub-rede recebem acesso ao namespace de hubs de eventos. Os hubs de eventos em si nunca estabelecem conexões de saída, não precisam obter acesso e, portanto, nunca concedem acesso à sua sub-rede habilitando essa regra.

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Criar uma regra de rede virtual com modelos de Azure Resource Manager

O modelo do Resource Manager a seguir permite adicionar uma regra de rede virtual a um namespace existente de hubs de eventos.

Parâmetros do modelo:

* **NamespaceName**: namespace de hubs de eventos.
* **vnetRuleName**: nome da regra de rede virtual a ser criada.
* **virtualNetworkingSubnetId**: caminho do Gerenciador de recursos totalmente qualificado para a sub-rede da rede virtual; por exemplo, `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` para a sub-rede padrão de uma rede virtual.

> [!NOTE]
> Embora não haja nenhuma regra de negação possível, o modelo de Azure Resource Manager tem a ação padrão definida como **"permitir"** , que não restringe as conexões.
> Ao tornar as regras de rede virtual ou firewalls, devemos alterar o ***"DefaultAction"***
> 
> De
> ```json
> "defaultAction": "Allow"
> ```
> para
> ```json
> "defaultAction": "Deny"
> ```
>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
        }
      },
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('eventhubNamespaceName')]",
        "type": "Microsoft.EventHub/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
        },
        "properties": { }
      },
      {
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.EventHub"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Para implantar o modelo, siga as instruções para [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre redes virtuais, consulte os links a seguir:

- [Pontos de extremidade de serviço de rede virtual do Azure][vnet-sep]
- [Filtragem de IP dos hubs de eventos do Azure][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: event-hubs-ip-filtering.md
