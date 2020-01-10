---
title: Regras de firewall dos hubs de eventos do Azure | Microsoft Docs
description: Use regras de firewall para permitir conexões de endereços IP específicos para os hubs de eventos do Azure.
services: event-hubs
documentationcenter: ''
author: spelluru
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.custom: seodec18
ms.topic: article
ms.date: 12/20/2019
ms.author: spelluru
ms.openlocfilehash: a988fbb089bd94456e0b91b377574ab27a67617f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437199"
---
# <a name="azure-event-hubs---use-firewall-rules"></a>Hubs de eventos do Azure – usar regras de firewall

Para cenários nos quais os hubs de eventos do Azure devem ser acessíveis somente de determinados sites conhecidos, as regras de firewall permitem que você configure regras para aceitar o tráfego originado de endereços IPv4 específicos. Por exemplo, estes endereços podem ser de um gateway empresarial do NAT.

## <a name="when-to-use"></a>Quando utilizar

Se você estiver procurando configurar seu namespace de hubs de eventos, de modo que ele deve receber tráfego apenas de um intervalo especificado de endereços IP e rejeitar tudo o mais, você pode aproveitar uma *regra de firewall* para bloquear pontos de extremidade do hub de eventos de outros endereços IP. Por exemplo, se você usar hubs de eventos com o [Azure Express Route][express-route], poderá criar uma *regra de firewall* para restringir o tráfego de seus endereços IP de infraestrutura local.

## <a name="how-filter-rules-are-applied"></a>Como são aplicadas as regras de filtro

As regras de filtro IP são aplicadas ao nível do espaço de nomes de Hubs de eventos. Por conseguinte, as regras são aplicadas a todas as ligações de clientes usando qualquer protocolo suportado.

Qualquer tentativa de conexão de um endereço IP que não corresponda a uma regra de IP permitida no namespace dos hubs de eventos é rejeitada como não autorizada. A resposta não menciona a regra IP.

## <a name="default-setting"></a>Definição predefinida

Por predefinição, o **filtro IP** grelha no portal dos Hubs de eventos está vazia. Essa configuração padrão significa que o seu hub de eventos aceita ligações a partir de qualquer endereço IP. Essa configuração padrão é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Avaliação da regra de filtro IP

Regras do filtro de IP serão aplicadas por ordem e a primeira regra que corresponde ao endereço IP determina a ação de aceitar ou rejeitar.

>[!WARNING]
> A implementação de firewalls pode impedir que outros serviços do Azure interajam com os hubs de eventos.
>
> Os serviços confiáveis da Microsoft não têm suporte quando a filtragem de IP (firewalls) é implementada e será disponibilizada em breve.
>
> Cenários comuns do Azure que não funcionam com a filtragem de IP (Observe que a lista **não** é exaustiva) –
> - Azure Stream Analytics
> - Integração com a grade de eventos do Azure
> - Rotas do Hub IoT do Azure
> - Device Explorer de IoT do Azure
>
> Os serviços da Microsoft a seguir devem estar em uma rede virtual
> - Aplicações Web do Azure
> - Funções do Azure

### <a name="creating-a-firewall-rule-with-azure-resource-manager-templates"></a>Criando uma regra de firewall com modelos de Azure Resource Manager

> [!IMPORTANT]
> As regras de firewall têm suporte em camadas **padrão** e **dedicadas** dos hubs de eventos. Não é suportada no escalão básico.

O modelo do Resource Manager seguinte permite adicionar uma regra de filtro IP para um espaço de nomes de Hubs de eventos existente.

Parâmetros do modelo:

- **ipMask** é um único endereço IPv4 ou um bloco de endereços IP na notação CIDR. Por exemplo, no CIDR notação 70.37.104.0/24 representa os 256 endereços IPv4 de 70.37.104.0 para 70.37.104.255, com o que indica o número de bits de prefixo significativo para o intervalo de 24.

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
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
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
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": [<YOUR EXISTING VIRTUAL NETWORK RULES>],
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Para implantar o modelo, siga as instruções para [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Passos seguintes

Para obter acesso omezující aos Hubs de eventos para redes virtuais do Azure, consulte a seguinte hiperligação:

- [Pontos de extremidade de serviço de rede virtual para hubs de eventos][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: event-hubs-service-endpoints.md
