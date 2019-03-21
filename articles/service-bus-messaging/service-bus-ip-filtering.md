---
title: Regras de Firewall de barramento de serviço do Azure | Documentos da Microsoft
description: Como utilizar as regras de Firewall para permitir ligações a partir de endereços IP específicos para o Azure Service Bus.
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 52475a3976ef8897ffed07c499eb06e7476c0156
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58136298"
---
# <a name="use-firewall-rules"></a>Utilize regras de Firewall

Para cenários nos quais o Azure Service Bus só é acessível a partir de certos sites bem conhecidos, as regras de Firewall permitem-lhe configurar regras para aceitar o tráfego proveniente de endereços de IPv4 específicos. Por exemplo, estes endereços podem ser de um gateway empresarial do NAT.

## <a name="when-to-use"></a>Quando utilizar

Se estiver à procura para a configuração do Service Bus, de modo que ele deve receber o tráfego apenas a partir de um especificado intervalo de endereços IP e rejeitar tudo o resto, em seguida, pode tirar partido de uma *Firewall* para bloquear os pontos finais do Service Bus a partir de outros endereços IP. Por exemplo, estiver a utilizar o Service Bus com [Azure Express Route] [ express-route] criar ligações privadas para a sua infraestrutura no local. 

## <a name="how-filter-rules-are-applied"></a>Como são aplicadas as regras de filtro

As regras de filtro IP são aplicadas ao nível do espaço de nomes do Service Bus. Por conseguinte, as regras são aplicadas a todas as ligações de clientes usando qualquer protocolo suportado.

Qualquer tentativa de ligação de um endereço IP que não corresponde a uma regra IP permitida no espaço de nomes é rejeitado como o Service Bus não autorizado. A resposta não menciona a regra IP.

## <a name="default-setting"></a>Definição predefinida

Por predefinição, o **filtro IP** grelha no portal do Service Bus está vazia. Essa configuração padrão significa que o seu espaço de nomes aceita ligações de qualquer endereço IP. Essa configuração padrão é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Avaliação da regra de filtro IP

Regras do filtro de IP serão aplicadas por ordem e a primeira regra que corresponde ao endereço IP determina a ação de aceitar ou rejeitar.

>[!WARNING]
> Implementar as regras de Firewall pode impedir que outros serviços do Azure a interagir com o Service Bus.
>
> Confiável a serviços da Microsoft não são suportados quando filtragem de IP (regras de Firewall) são implementados e será disponibilizada em breve.
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
> - Serviço de Aplicações do Azure
> - Funções do Azure

### <a name="creating-a-virtual-network-and-firewall-rule-with-azure-resource-manager-templates"></a>Criar uma regra de rede e de firewall virtual com modelos Azure Resource Manager

> [!IMPORTANT]
> Firewalls e redes virtuais são suportados apenas na **premium** escalão do Service Bus.

O modelo do Resource Manager seguinte permite adicionar uma regra de rede virtual para um espaço de nomes do barramento de serviço existente.

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
          "name": "Standard",
          "tier": "Standard"
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

Para implementar o modelo, siga as instruções para [do Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Passos Seguintes

Para obter acesso omezující ao Service Bus para redes virtuais do Azure, consulte a seguinte hiperligação:

- [Pontos finais de serviço de rede virtual para o Service Bus][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
