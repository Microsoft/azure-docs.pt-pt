---
title: Regras de Firewall do Evento Azure Hubs Microsoft Docs
description: Utilize regras de firewall para permitir ligações de endereços IP específicos para Hubs de Eventos Azure.
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
ms.openlocfilehash: fb11d1bdcf8145d4e78285833789b41c92b0ce4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064880"
---
# <a name="configure-ip-firewall-rules-for-an-azure-event-hubs-namespace"></a>Configure regras de firewall IP para um espaço de nome do Azure Event Hubs
Por predefinição, os espaços de nome do Event Hubs são acessíveis a partir da internet desde que o pedido venha com autenticação e autorização válidas. Com firewall IP, pode restringi-lo ainda mais a apenas um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Esta funcionalidade é útil em cenários em que os Hubs de Eventos Azure devem ser acessíveis apenas a partir de certos sites bem conhecidos. As regras de firewall permitem-lhe configurar regras para aceitar o tráfego originário de endereços IPv4 específicos. Por exemplo, se utilizar os Hubs de Eventos com [a Rota Expresso Azure,][express-route]pode criar uma regra de **firewall** para permitir o tráfego a partir apenas dos seus endereços IP de infraestrutura no local. 

## <a name="ip-firewall-rules"></a>Regras de firewall IP
As regras de firewall IP são aplicadas ao nível do espaço de nome do Event Hubs. Por isso, as regras aplicam-se a todas as ligações dos clientes que utilizam qualquer protocolo suportado. Qualquer tentativa de ligação a partir de um endereço IP que não corresponda a uma regra ip permitida no espaço de nome do Event Hubs é rejeitada como não autorizada. A resposta não menciona a regra do PI. As regras do filtro IP são aplicadas por ordem, e a primeira regra que corresponde ao endereço IP determina a aceitação ou rejeita a ação.

## <a name="use-azure-portal"></a>Utilizar o portal do Azure
Esta secção mostra-lhe como usar o portal Azure para criar regras de firewall IP para um espaço de nome de Event Hubs. 

1. Navegue para o seu espaço de **nome Sem Nome Do seu Evento Hubs** no [portal Azure](https://portal.azure.com).
2. No menu esquerdo, selecione a opção **Networking.** Se selecionar a opção **Todas as redes,** o hub do evento aceita ligações a partir de qualquer endereço IP. Esta definição equivale a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0. 

    ![Firewall - Todas as redes selecionadas](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Para restringir o acesso a redes específicas e endereços IP, selecione a opção **redes Selecionadas.** Na secção **Firewall,** siga estes passos:
    1. Selecione Adicionar a opção de **endereço IP do seu cliente** para dar ao seu cliente atual IP o acesso ao espaço de nome. 
    2. Para o intervalo de **endereços,** introduza um endereço IPv4 específico ou um conjunto de endereços IPv4 na notação CIDR. 
    3. Especifique se pretende **permitir que serviços microsoft fidedignos contornem esta firewall**. 

        ![Firewall - Todas as redes selecionadas](./media/event-hubs-firewall/firewall-selected-networks-trusted-access-disabled.png)
3. Selecione **Guardar** na barra de ferramentas para guardar as definições. Aguarde alguns minutos para que a confirmação apareça nas notificações do portal.


## <a name="use-resource-manager-template"></a>Utilizar o modelo do Resource Manager

> [!IMPORTANT]
> As regras de firewall são suportadas em níveis **padrão** e **dedicados** de Centros de Eventos. Não é suportado no nível básico.

O seguinte modelo de Gestor de Recursos permite adicionar uma regra de filtro IP a um espaço de nome existente para os Hubs de Eventos.

Parâmetros do modelo:

- **ipMask** é um único endereço IPv4 ou um bloco de endereços IP na notação CIDR. Por exemplo, no ponto de notação do CIDR 70.37.104.0/24 representa os 256 endereços IPv4 de 70.37.104.0 a 70.37.104.255, com 24 indicando o número de peças prefixos significativas para a gama.

> [!NOTE]
> Embora não existam regras de negação possíveis, o modelo do Gestor de Recursos Do Azure tem o conjunto de ação padrão para **"Permitir"** que não restringe as ligações.
> Ao fazer regras de Rede Virtual ou Firewalls, temos de alterar a ***"defaultAction"***
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
          "trustedServiceAccessEnabled": false,
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Para implementar o modelo, siga as instruções para o Gestor de [Recursos Azure][lnk-deploy].

## <a name="next-steps"></a>Passos seguintes

Para limitar o acesso aos Centros de Eventos às redes virtuais Azure, consulte o seguinte link:

- [Pontos finais do serviço de rede virtual para hubs de eventos][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: event-hubs-service-endpoints.md
