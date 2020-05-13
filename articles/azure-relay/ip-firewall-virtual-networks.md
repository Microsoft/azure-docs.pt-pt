---
title: Firewall IP Congigure para espaço de nome de Retransmissão Azure
description: Este artigo descreve como utilizar regras de firewall para permitir ligações de endereços IP específicos a espaços de nome sinuosos do Azure Relay.
services: service-bus-relay
documentationcenter: ''
author: spelluru
ms.service: service-bus-relay
ms.devlang: na
ms.custom: seodec18
ms.topic: article
ms.date: 05/07/2020
ms.author: spelluru
ms.openlocfilehash: e2a9e89ca8c3b77b09a58a45959f1acda1457b56
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211057"
---
# <a name="configure-ip-firewall-for-an-azure-relay-namespace"></a>Configure firewall IP para um espaço de nome de Retransmissão Azure
Por predefinição, os espaços de nome retransmissão são acessíveis a partir da internet desde que o pedido venha com autenticação e autorização válidas. Com firewall IP, pode restringi-lo ainda mais a apenas um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Esta funcionalidade é útil em cenários em que o Azure Relay só deve ser acessível a partir de certos sites bem conhecidos. As regras de firewall permitem-lhe configurar regras para aceitar o tráfego originário de endereços IPv4 específicos. Por exemplo, se utilizar o Relay com a [Rota Expresso Azure,](../expressroute/expressroute-faqs.md#supported-services)pode criar uma regra de **firewall** para permitir o tráfego a partir apenas dos seus endereços IP de infraestrutura no local. 


> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. 


## <a name="enable-ip-firewall-rules"></a>Ativar as regras de firewall IP
As regras de firewall IP são aplicadas ao nível do espaço de nome. Por isso, as regras aplicam-se a todas as ligações dos clientes que utilizam qualquer protocolo suportado. Qualquer tentativa de ligação de um endereço IP que não corresponda a uma regra ip permitida no espaço de nome é rejeitada como não autorizada. A resposta não menciona a regra do PI. As regras do filtro IP são aplicadas por ordem, e a primeira regra que corresponde ao endereço IP determina a aceitação ou rejeita a ação.

### <a name="use-azure-portal"></a>Utilizar o portal do Azure
Esta secção mostra-lhe como usar o portal Azure para criar regras de firewall IP para um espaço de nome. 

1. Navegue para o seu espaço de **nome relé** no [portal Azure](https://portal.azure.com).
2. No menu esquerdo, selecione a opção **Networking.** Se selecionar a opção **Todas as redes** na secção Permitir aceder a partir **da** secção, o espaço de nome retransmissor aceita ligações a partir de qualquer endereço IP. Esta definição equivale a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0. 

    ![Firewall - Todas as redes selecionadas](./media/ip-firewall/all-networks-selected.png)
1. Para restringir o acesso a redes específicas e endereços IP, selecione a opção **redes Selecionadas.** Na secção **Firewall,** siga estes passos:
    1. Selecione Adicionar a opção de **endereço IP do seu cliente** para dar ao seu cliente atual IP o acesso ao espaço de nome. 
    2. Para o intervalo de **endereços,** introduza um endereço IPv4 específico ou um conjunto de endereços IPv4 na notação CIDR. 
    3. Especifique se pretende **permitir que serviços microsoft fidedignos contornem esta firewall**. 

        ![Firewall - Todas as redes selecionadas](./media/ip-firewall/selected-networks-trusted-access-disabled.png)
3. Selecione **Guardar** na barra de ferramentas para guardar as definições. Aguarde alguns minutos para que a confirmação apareça nas notificações do portal.


### <a name="use-resource-manager-template"></a>Utilizar o modelo do Resource Manager
O seguinte modelo de Gestor de Recursos permite adicionar uma regra de filtro IP a um espaço de nome de retransmissão existente.

O modelo requer um parâmetro: **ipMask,** que é um único endereço IPv4 ou um bloco de endereços IP na notação CIDR. Por exemplo, no ponto de notação do CIDR 70.37.104.0/24 representa os 256 endereços IPv4 de 70.37.104.0 a 70.37.104.255, com 24 indicando o número de peças prefixos significativas para a gama.

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
      "relayNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Relay namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('relayNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('relayNamespaceName')]",
        "type": "Microsoft.Relay/namespaces",
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
        "type": "Microsoft.Relay/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.Relay/namespaces/', parameters('relayNamespaceName'))]"
        ],
        "properties": {
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

Para implementar o modelo, siga as instruções para o Gestor de [Recursos Azure](../azure-resource-manager/templates/deploy-powershell.md).



## <a name="next-steps"></a>Passos seguintes
Para conhecer outras funcionalidades relacionadas com a segurança da rede, consulte a [segurança da Rede.](network-security.md)


<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]:: 
