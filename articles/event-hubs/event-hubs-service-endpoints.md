---
title: Virtual rede pontos finais de serviço - Event Hubs do Azure | Documentos da Microsoft
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
ms.openlocfilehash: 2ac89444bde4e2efc918aced9d76c099eb792557
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75966002"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Utilizar pontos finais de serviço de rede Virtual com o Event Hubs do Azure

A integração dos hubs de eventos com [pontos de extremidade de serviço de rede virtual (VNet)][vnet-sep] permite o acesso seguro a recursos de mensagens de cargas de trabalho, como máquinas virtuais associadas a redes virtuais, com o caminho de tráfego de rede protegido em ambas as extremidades.

Uma vez configurado para ser associado a pelo menos um ponto de extremidade de serviço de sub-rede de rede virtual, o respectivo namespace de hubs de eventos não aceitará mais o tráfego de qualquer lugar, mas de sub-redes autorizadas em redes virtuais. Da perspectiva de rede virtual, ligando um espaço de nomes de Hubs de eventos a um ponto de extremidade de serviço configura um túnel de rede isolado da sub-rede da rede virtual para o serviço de mensagens. 

O resultado é uma relação isolada e privada entre as cargas de trabalho ligada à sub-rede e o namespace respectivo do Hubs de eventos, apesar do endereço de rede observable do sistema de mensagens serviço ponto final que está a ser num intervalo IP público. Há uma exceção a esse comportamento. Habilitar um ponto de extremidade de serviço, por padrão, habilita a regra denyall no firewall IP associado à rede virtual. Você pode adicionar endereços IP específicos no firewall de IP para habilitar o acesso ao ponto de extremidade público do hub de eventos. 


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
> Redes virtuais são suportadas no **padrão** e **dedicado** escalões de Hubs de eventos. Não é suportada no escalão básico.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Cenários de segurança avançada ativados pela integração de VNet 

Soluções que exigem segurança rígida e segmentada e onde as sub-redes da rede virtual fornecem a segmentação entre os serviços compartimentalizados, ainda precisam de caminhos de comunicação entre os serviços que residem nesses compartimentos.

Qualquer rotas IP imediata entre compartimentos, incluindo aquelas com HTTPS por TCP/IP, carrega o risco de exploração de vulnerabilidades da camada de rede em segurança. Serviços de mensagens fornecem caminhos de comunicação completamente isolado, onde as mensagens até mesmo são escritas no disco à medida que eles fazem a transição entre partes. Cargas de trabalho em duas redes virtuais diferentes que estão ambos vinculadas para a mesma instância de Hubs de eventos podem comunicar com eficiência e fiável através de mensagens, enquanto a integridade de limite de isolamento de rede correspondentes é preservada.
 
Isso significa que a segurança confidencial soluções na cloud não ganhará acesso apenas às capacidades do Azure líder da indústria fiáveis e escaláveis assíncronas mensagens, mas agora podem utilizar mensagens para criar caminhos de comunicação entre a solução segura compartments que são inerentemente mais seguro do que o que é conseguido com qualquer modo de comunicação de ponto-a-ponto, incluindo o HTTPS e outros protocolos de socket protegida por TLS.

## <a name="bind-event-hubs-to-virtual-networks"></a>Ligar Hubs de eventos para redes virtuais

*Regras de rede virtual* são o recurso de segurança de firewall que controla se o seu espaço de nomes de Hubs de eventos do Azure aceita ligações a partir de uma sub-rede de rede virtual específico.

Um espaço de nomes de Hubs de eventos de enlace a uma rede virtual é um processo de dois passos. Primeiro, você precisa criar um **ponto de extremidade de serviço de rede virtual** em uma sub-rede de rede virtual e habilitá-la para "Microsoft. EventHub", conforme explicado na [visão geral do ponto de extremidade do serviço][vnet-sep]. Depois de adicionar o ponto final de serviço, vincular o espaço de nomes de Hubs de eventos ao mesmo com um *regra de rede virtual*.

A regra de rede virtual é uma associação do namespace de hubs de eventos com uma sub-rede de rede virtual. Embora exista a regra, todas as cargas de trabalho ligadas à sub-rede são concedidas acesso ao espaço de nomes dos Hubs de eventos. Os Hubs de eventos em si nunca estabelece as ligações de saída, não precisa de obter acesso e é, portanto, nunca concedida acesso à sua sub-rede, permitindo que esta regra.

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Criar uma regra de rede virtual com modelos Azure Resource Manager

O modelo do Resource Manager seguinte permite adicionar uma regra de rede virtual para um espaço de nomes de Hubs de eventos existente.

Parâmetros do modelo:

* **namespaceName**: espaço de nomes de Hubs de eventos.
* **vnetRuleName**: nome para a regra de rede Virtual a ser criada.
* **virtualNetworkingSubnetId**: caminho totalmente qualificado do Resource Manager para a sub-rede de rede virtual; por exemplo, `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` para a sub-rede de predefinição de uma rede virtual.

> [!NOTE]
> Embora não haja nenhuma regra de negação possível, o modelo de Azure Resource Manager tem a ação padrão definida como **"permitir"** , que não restringe as conexões.
> Ao tornar as regras de rede virtual ou firewalls, devemos alterar o ***"DefaultAction"***
> 
> de
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

Para obter mais informações sobre as redes virtuais, consulte as seguintes ligações:

- [Pontos de extremidade de serviço de rede virtual do Azure][vnet-sep]
- [Filtragem de IP dos hubs de eventos do Azure][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: event-hubs-ip-filtering.md
