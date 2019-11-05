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
ms.date: 12/06/2018
ms.author: spelluru
ms.openlocfilehash: f96c25dbb85ed92141636487f10d861a8c5e5f28
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468434"
---
# <a name="use-firewall-rules"></a>Usar regras de firewall

Para cenários nos quais os hubs de eventos do Azure devem ser acessíveis somente de determinados sites conhecidos, as regras de firewall permitem que você configure regras para aceitar o tráfego originado de endereços IPv4 específicos. Por exemplo, esses endereços podem ser aqueles de um gateway de NAT corporativo.

## <a name="when-to-use"></a>Quando utilizar

Se você estiver procurando configurar seu namespace de hubs de eventos, de modo que ele deve receber tráfego apenas de um intervalo especificado de endereços IP e rejeitar tudo o mais, você pode aproveitar uma *regra de firewall* para bloquear pontos de extremidade do hub de eventos de outros endereços IP. Por exemplo, se você usar hubs de eventos com o [Azure Express Route][express-route], poderá criar uma *regra de firewall* para restringir o tráfego de seus endereços IP de infraestrutura local.

## <a name="how-filter-rules-are-applied"></a>Como as regras de filtro são aplicadas

As regras de filtro IP são aplicadas no nível de namespace de hubs de eventos. Portanto, as regras se aplicam a todas as conexões de clientes usando qualquer protocolo com suporte.

Qualquer tentativa de conexão de um endereço IP que não corresponda a uma regra de IP permitida no namespace dos hubs de eventos é rejeitada como não autorizada. A resposta não menciona a regra de IP.

## <a name="default-setting"></a>Configuração padrão

Por padrão, a grade de **filtro IP** no portal para os hubs de eventos está vazia. Essa configuração padrão significa que o Hub de eventos aceita conexões de qualquer endereço IP. Essa configuração padrão é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Avaliação da regra de filtro IP

As regras de filtro IP são aplicadas em ordem, e a primeira regra que corresponde ao endereço IP determina a ação de aceitar ou rejeitar.

>[!WARNING]
> A implementação de firewalls pode impedir que outros serviços do Azure interajam com os hubs de eventos.
>
> Os serviços confiáveis da Microsoft não têm suporte quando a filtragem de IP (firewalls) é implementada e será disponibilizada em breve.
>
> Cenários comuns do Azure que não funcionam com a filtragem de IP (Observe que a lista **não** é exaustiva) –
> - Azure Monitor
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
> As regras de firewall têm suporte em camadas **padrão** e **dedicadas** dos hubs de eventos. Não há suporte na camada básica.

O modelo do Resource Manager a seguir permite adicionar uma regra de filtro IP a um namespace de hubs de eventos existente.

Parâmetros do modelo:

- **ipMask** é um único endereço IPv4 ou um bloco de endereços IP na notação CIDR. Por exemplo, na notação CIDR 70.37.104.0/24 representa os endereços IPv4 256 de 70.37.104.0 para 70.37.104.255, com 24 indicando o número de bits de prefixo significativos para o intervalo.

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

Para restringir o acesso aos hubs de eventos para redes virtuais do Azure, consulte o seguinte link:

- [Pontos de extremidade de serviço de rede virtual para hubs de eventos][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: event-hubs-service-endpoints.md
