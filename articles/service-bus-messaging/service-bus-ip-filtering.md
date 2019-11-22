---
title: Regras de firewall do barramento de serviço do Azure | Microsoft Docs
description: Como usar regras de firewall para permitir conexões de endereços IP específicos para o barramento de serviço do Azure.
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 04/23/2019
ms.author: aschhab
ms.openlocfilehash: 02d6e150e638321e11a8dec9838e360faa00783e
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280947"
---
# <a name="use-firewall-rules"></a>Usar regras de firewall

Para cenários nos quais o barramento de serviço do Azure só é acessível de determinados sites conhecidos, as regras de firewall permitem que você configure regras para aceitar o tráfego originado de endereços IPv4 específicos. Por exemplo, estes endereços podem ser de um gateway empresarial do NAT.

## <a name="when-to-use"></a>Quando utilizar

Se você estiver procurando configurar o barramento de serviço de modo que ele deva receber tráfego somente de um intervalo especificado de endereços IP e rejeitar tudo o mais, você pode aproveitar um *Firewall* para bloquear pontos de extremidade do barramento de serviço de outros endereços IP. Por exemplo, você está usando o barramento de serviço com o [Azure Express Route][express-route] para criar conexões privadas com sua infraestrutura local. 

## <a name="how-filter-rules-are-applied"></a>Como são aplicadas as regras de filtro

As regras de filtro IP são aplicadas no nível de namespace do barramento de serviço. Por conseguinte, as regras são aplicadas a todas as ligações de clientes usando qualquer protocolo suportado.

Qualquer tentativa de conexão de um endereço IP que não corresponda a uma regra de IP permitida no namespace do barramento de serviço é rejeitada como não autorizada. A resposta não menciona a regra IP.

## <a name="default-setting"></a>Definição predefinida

Por padrão, a grade de **filtro IP** no portal para o barramento de serviço está vazia. Essa configuração padrão significa que o namespace aceita conexões com qualquer endereço IP. Essa configuração padrão é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Avaliação da regra de filtro IP

As regras de filtro IP são aplicadas em ordem e a primeira regra que corresponde ao endereço IP determina a ação aceitar ou rejeitar.

>[!WARNING]
> A implementação de regras de firewall pode impedir que outros serviços do Azure interajam com o barramento de serviço.
>
> Os serviços confiáveis da Microsoft não têm suporte quando a filtragem de IP (regras de firewall) são implementadas e será disponibilizada em breve.
>
> Cenários comuns do Azure que não funcionam com a filtragem de IP (Observe que a lista **não** é exaustiva) –
> - Azure Stream Analytics
> - Integração com a grade de eventos do Azure
> - Rotas do Hub IoT do Azure
> - Device Explorer de IoT do Azure
>
> Os serviços da Microsoft a seguir devem estar em uma rede virtual
> - Serviço de Aplicações do Azure
> - Funções do Azure

### <a name="creating-a-virtual-network-and-firewall-rule-with-azure-resource-manager-templates"></a>Criando uma regra de firewall e rede virtual com modelos de Azure Resource Manager

> [!IMPORTANT]
> Só há suporte para firewalls e redes virtuais na camada **Premium** do barramento de serviço.

O modelo do Resource Manager a seguir permite adicionar uma regra de rede virtual a um namespace do barramento de serviço existente.

Parâmetros do modelo:

- **ipMask** é um único endereço IPv4 ou um bloco de endereços IP na notação CIDR. Por exemplo, no CIDR notação 70.37.104.0/24 representa os 256 endereços IPv4 de 70.37.104.0 para 70.37.104.255, com o que indica o número de bits de prefixo significativo para o intervalo de 24.

> [!NOTE]
> Embora não haja nenhuma regra de negação possível, o modelo de Azure Resource Manager tem a ação padrão definida como **"permitir"** , que não restringe as conexões.
> Ao tornar as regras de rede virtual ou firewalls, devemos alterar o ***"DefaultAction"***
> 
> from
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
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('servicebusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Premium",
          "tier": "Premium"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
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

Para restringir o acesso ao barramento de serviço para redes virtuais do Azure, consulte o seguinte link:

- [Pontos de extremidade de serviço de rede virtual para o barramento de serviço][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
