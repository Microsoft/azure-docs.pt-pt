---
title: Regras de Firewall de Hubs de eventos do Azure | Documentos da Microsoft
description: Utilize as regras de Firewall para permitir ligações a partir de endereços IP específicos para os Hubs de eventos do Azure.
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
ms.openlocfilehash: ccb2fa7b0805b332957513c52c0c1051d068d2cc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821669"
---
# <a name="use-firewall-rules"></a>Utilize regras de Firewall

Para cenários em que os Hubs de eventos do Azure deve ser apenas acessíveis a partir de certos sites bem conhecidos, as regras de firewall permitem-lhe configurar regras para aceitar o tráfego proveniente de endereços de IPv4 específicos. Por exemplo, estes endereços podem ser de um gateway empresarial do NAT.

## <a name="when-to-use"></a>Quando utilizar

Se quiser para configurar o seu espaço de nomes de Hubs de eventos, de modo que devem receber o tráfego de apenas um especificado intervalo de endereços IP e rejeitar tudo o resto, em seguida, pode tirar partido de uma *regra de Firewall* para bloquear os pontos finais do Hub de eventos do outros endereços IP. Por exemplo, se utilizar os Hubs de eventos com [Azure Express Route][express-route], pode criar um *regra de Firewall* para restringir o tráfego de seu IP de infraestrutura no local endereços.

## <a name="how-filter-rules-are-applied"></a>Como são aplicadas as regras de filtro

As regras de filtro IP são aplicadas ao nível do espaço de nomes de Hubs de eventos. Por conseguinte, as regras são aplicadas a todas as ligações de clientes usando qualquer protocolo suportado.

Qualquer tentativa de ligação de um endereço IP que não corresponde a uma regra IP permitida no espaço de nomes é rejeitado como os Hubs de eventos não autorizado. A resposta não menciona a regra IP.

## <a name="default-setting"></a>Definição predefinida

Por predefinição, o **filtro IP** grelha no portal dos Hubs de eventos está vazia. Essa configuração padrão significa que o seu hub de eventos aceita ligações a partir de qualquer endereço IP. Essa configuração padrão é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Avaliação da regra de filtro IP

Regras do filtro de IP serão aplicadas por ordem e a primeira regra que corresponde ao endereço IP determina a ação de aceitar ou rejeitar.

>[!WARNING]
> Implementação de Firewalls pode impedir que outros serviços do Azure a interagir com os Hubs de eventos.
>
> Confiável a serviços da Microsoft não são suportados quando filtragem de IP (Firewalls) são implementados e será disponibilizada em breve.
>
> Cenários comuns do Azure que não funcionam com a filtragem de IP (tenha em atenção que a lista está **não** exaustiva)-
> - Azure Monitor
> - Azure Stream Analytics
> - Integração com o Azure Event Grid
> - Encaminha o Hub IoT do Azure
> - O Azure IoT Device Explorer
> - Azure Data Explorer
>
> O abaixo Microsoft serviços são necessários para estar numa rede virtual
> - Aplicações Web do Azure
> - Funções do Azure

### <a name="creating-a-firewall-rule-with-azure-resource-manager-templates"></a>Criar uma regra de Firewall com modelos Azure Resource Manager

> [!IMPORTANT]
> Regras de firewall são suportadas no **padrão** e **dedicado** escalões de Hubs de eventos. Não é suportada no escalão básico.

O modelo do Resource Manager seguinte permite adicionar uma regra de filtro IP para um espaço de nomes de Hubs de eventos existente.

Parâmetros do modelo:

- **ipMask** é um único endereço IPv4 ou um bloco de endereços IP na notação CIDR. Por exemplo, no CIDR notação 70.37.104.0/24 representa os 256 endereços IPv4 de 70.37.104.0 para 70.37.104.255, com o que indica o número de bits de prefixo significativo para o intervalo de 24.

> [!NOTE]
> Embora não haja nenhuma regra de negação possível, o modelo Azure Resource Manager, tem a ação padrão definida como **"Permitir"** que não restringem as ligações.
> Ao fazer as regras de rede Virtual ou Firewalls, devemos alterar o ***"defaultAction"***
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

Para implementar o modelo, siga as instruções para [do Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Passos Seguintes

Para obter acesso omezující aos Hubs de eventos para redes virtuais do Azure, consulte a seguinte hiperligação:

- [Pontos finais de serviço de rede virtual para os Hubs de eventos][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: event-hubs-service-endpoints.md
