---
title: Configure firewall IP para espaço de nome do Azure Relay
description: Este artigo descreve como utilizar regras de firewall para permitir ligações de endereços IP específicos a espaços de nomes Azure Relay.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: ad8feed5df49dcc4503226a5fae50195bb9d48aa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91999504"
---
# <a name="configure-ip-firewall-for-an-azure-relay-namespace"></a>Configure firewall IP para um espaço de nome Azure Relay
Por predefinição, os espaços de nome retransmissores estão acessíveis a partir da Internet desde que o pedido venha com autenticação e autorização válidas. Com a firewall IP, pode restringi-lo ainda mais a um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (Classless Inter-Domain Encaminhamento).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Esta funcionalidade é útil em cenários em que o Azure Relay só deve ser acessível a partir de determinados sites conhecidos. As regras de firewall permitem-lhe configurar regras para aceitar tráfego originário de endereços IPv4 específicos. Por exemplo, se utilizar o Relay com [a Rota Expresso Azure,](../expressroute/expressroute-faqs.md#supported-services)pode criar uma regra de **firewall** para permitir o tráfego a partir apenas dos endereços IP da infraestrutura no local. 


> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. 


## <a name="enable-ip-firewall-rules"></a>Ativar as regras de firewall IP
As regras de firewall IP são aplicadas ao nível do espaço de nome. Por isso, as regras aplicam-se a todas as ligações dos clientes utilizando qualquer protocolo suportado. Qualquer tentativa de ligação a partir de um endereço IP que não corresponda a uma regra ip permitida no espaço de nomes é rejeitada como não autorizada. A resposta não menciona a regra do IP. As regras do filtro IP são aplicadas por ordem, e a primeira regra que corresponde ao endereço IP determina a ação de aceitação ou rejeição.

### <a name="use-azure-portal"></a>Utilizar o portal do Azure
Esta secção mostra-lhe como usar o portal Azure para criar regras de firewall IP para um espaço de nome. 

1. Navegue para o seu **espaço de nome retransitia** no [portal Azure.](https://portal.azure.com)
2. No menu esquerdo, selecione A opção **de Rede.** Se selecionar a opção **Todas as redes** na secção Permitir o acesso a partir  **da** secção, o espaço de nome Relay aceita ligações a partir de qualquer endereço IP. Esta definição é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0. 

    ![A screenshot mostra a página de Networking com a opção Todas as redes selecionadas.](./media/ip-firewall/all-networks-selected.png)
1. Para restringir o acesso a redes específicas e endereços IP, selecione a opção **redes Selecionadas.** Na secção **Firewall,** siga estes passos:
    1. Selecione Adicionar a opção **de endereço IP** do seu cliente para dar ao seu cliente atual IP o acesso ao espaço de nome. 
    2. Para **o intervalo de endereços**, insira um endereço IPv4 específico ou um intervalo de endereço IPv4 na notação CIDR. 

        ![Firewall - Todas as opções de redes selecionadas](./media/ip-firewall/selected-networks-trusted-access-disabled.png)
3. **Selecione Guarde** na barra de ferramentas para guardar as definições. Aguarde alguns minutos para que a confirmação apareça nas notificações do portal.


### <a name="use-resource-manager-template"></a>Utilizar o modelo do Resource Manager
O modelo seguinte do Gestor de Recursos permite adicionar uma regra de filtro IP a um espaço de nome retransitado existente.

O modelo tem um parâmetro: **ipMask,** que é um único endereço IPv4 ou um bloco de endereços IP na notação CIDR. Por exemplo, na notação CIDR 70.37.104.0/24 representa os 256 endereços IPv4 de 70.37.104.0 a 70.37.104.255, com 24 indicando o número de bits prefixos significativos para a gama.

> [!NOTE]
> Embora não existam regras de negação possíveis, o modelo de Gestor de Recursos Azure tem a ação padrão definida para **"Permitir"** que não restringe as ligações.
> Ao fazer as regras de Rede Virtual ou Firewalls, temos de alterar o ***"defaultAction"***
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
      "namespaceNetworkRuleSetName": "[concat(parameters('relayNamespaceName'), concat('/', 'default'))]"
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
        "type": "Microsoft.Relay/namespaces/networkrulesets",
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
          "virtualNetworkRules": [],
          "trustedServiceAccessEnabled": false,
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Para implementar o modelo, siga as instruções para [O Gestor de Recursos Azure](../azure-resource-manager/templates/deploy-powershell.md).



## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre outras funcionalidades relacionadas com a segurança da rede, consulte [a segurança da rede](network-security.md).


<!-- Links -->

[express-route]:  ../expressroute/expressroute-faqs.md#supported-services
