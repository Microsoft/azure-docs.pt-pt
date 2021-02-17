---
title: Azure Event Hubs Firewall Rules | Microsoft Docs
description: Utilize regras de firewall para permitir ligações de endereços IP específicos para Azure Event Hubs.
ms.topic: article
ms.date: 02/12/2021
ms.openlocfilehash: ca5995c3e1b9923d925ddc4deae299c28261d18a
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560851"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-from-specific-ip-addresses-or-ranges"></a>Permitir o acesso aos espaços de nomes do Azure Event Hubs a partir de endereços ou intervalos IP específicos
Por predefinição, os espaços de nomes do Event Hubs estão acessíveis a partir da Internet desde que o pedido venha com autenticação e autorização válidas. Com a firewall IP, pode restringi-lo ainda mais a um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (Classless Inter-Domain Encaminhamento).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Esta funcionalidade é útil em cenários em que os Azure Event Hubs deverão ser acessíveis apenas a partir de determinados sites conhecidos. As regras de firewall permitem-lhe configurar regras para aceitar tráfego originário de endereços IPv4 específicos. Por exemplo, se utilizar Os Centros de Eventos com [Rota Azure Express,][express-route]pode criar uma regra de **firewall** para permitir o tráfego a partir apenas dos endereços IP da infraestrutura no local. 

>[!WARNING]
> Ligar as regras de firewall para o seu espaço de nome Event Hubs bloqueia os pedidos de entrada por padrão, a menos que os pedidos sejam originados de um serviço que opera a partir de endereços IP públicos permitidos. Os pedidos que estão bloqueados incluem os de outros serviços Azure, do portal Azure, de serviços de registo e métricas, e assim por diante. Como exceção, pode permitir o acesso aos recursos do Event Hubs a partir de certos serviços fidedignos, mesmo quando a filtragem IP está ativada. Para obter uma lista de serviços fidedignos, consulte [os serviços trusted Microsoft](#trusted-microsoft-services).

> [!IMPORTANT]
> Especifique pelo menos uma regra de IP ou rede virtual para o espaço de nomes para permitir o tráfego apenas a partir dos endereços IP especificados ou sub-rede de uma rede virtual. Se não houver regras de IP e rede virtual, o espaço de nomes pode ser acedido através da internet pública (utilizando a chave de acesso).  


## <a name="ip-firewall-rules"></a>Regras de firewall IP
As regras de firewall IP são aplicadas ao nível do espaço de nomes do Event Hubs. Assim, as regras aplicam-se a todas as ligações de clientes que utilizem qualquer protocolo suportado. Qualquer tentativa de ligação a partir de um endereço IP que não corresponda a uma regra de IP permitida no espaço de nomes Do Event Hubs é rejeitada como não autorizada. A resposta não menciona a regra do IP. As regras do filtro IP são aplicadas por ordem, e a primeira regra que corresponde ao endereço IP determina a ação de aceitação ou rejeição.

## <a name="use-azure-portal"></a>Utilizar o portal do Azure
Esta secção mostra-lhe como usar o portal Azure para criar regras de firewall IP para um espaço de nomes de Event Hubs. 

1. Navegue para o seu **espaço de nomes de Centros de Eventos** no [portal Azure.](https://portal.azure.com)
4. Selecione **rede em** **Definições** no menu esquerdo. Você vê o **separador Networking** apenas para espaços de nome **padrão** ou **dedicados.** 
    
    > [!WARNING]
    > Se selecionar a opção **redes selecionadas** e não adicionar pelo menos uma regra de firewall IP ou uma rede virtual nesta página, o espaço de nome pode ser acedido através da **internet pública** (utilizando a chave de acesso).  

    :::image type="content" source="./media/event-hubs-firewall/selected-networks.png" alt-text="Separador de redes - opção de redes selecionada" lightbox="./media/event-hubs-firewall/selected-networks.png":::    

    Se selecionar a opção **Todas as redes,** o centro de eventos aceita ligações a partir de qualquer endereço IP (utilizando a chave de acesso). Esta definição é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0. 

    ![Screenshot que mostra a página "Firewall e redes virtuais" com a opção "Todas as redes" selecionada.](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Para restringir o acesso a endereços IP específicos, confirme que a opção **de redes Selecionadas** está selecionada. Na secção **Firewall,** siga estes passos:
    1. Selecione Adicionar a opção **de endereço IP** do seu cliente para dar ao seu cliente atual IP o acesso ao espaço de nome. 
    2. Para **o intervalo de endereços**, insira um endereço IPv4 específico ou um intervalo de endereço IPv4 na notação CIDR. 

    >[!WARNING]
    > Se selecionar a opção **redes selecionadas** e não adicionar pelo menos uma regra de firewall IP ou uma rede virtual nesta página, o espaço de nome pode ser acedido através da internet pública (utilizando a chave de acesso).
1. Especificar se pretende **permitir que serviços de confiança da Microsoft contornem esta firewall.** Consulte [os serviços da Microsoft fidedignos](#trusted-microsoft-services) para obter mais detalhes. 

      ![Firewall - Todas as opções de redes selecionadas](./media/event-hubs-firewall/firewall-selected-networks-trusted-access-disabled.png)
3. **Selecione Guarde** na barra de ferramentas para guardar as definições. Aguarde alguns minutos para que a confirmação apareça nas notificações do portal.

    > [!NOTE]
    > Para restringir o acesso a redes virtuais específicas, consulte [permitir o acesso a redes específicas.](event-hubs-service-endpoints.md)

[!INCLUDE [event-hubs-trusted-services](../../includes/event-hubs-trusted-services.md)]


## <a name="use-resource-manager-template"></a>Utilizar o modelo do Resource Manager

> [!IMPORTANT]
> As regras de firewall são suportadas em níveis **padrão** e **dedicados** de Centros de Eventos. Não é suportado no nível básico.

O modelo seguinte do Gestor de Recursos permite adicionar uma regra de filtro IP a um espaço de nomes de Centros de Eventos existente.

**ipMask** no modelo é um único endereço IPv4 ou um bloco de endereços IP na notação CIDR. Por exemplo, na notação CIDR 70.37.104.0/24 representa os 256 endereços IPv4 de 70.37.104.0 a 70.37.104.255, com 24 indicando o número de bits prefixos significativos para a gama.

Ao adicionar regras de rede virtual ou firewalls, desa um valor de `defaultAction` `Deny` .

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
        "type": "Microsoft.EventHub/namespaces/networkrulesets",
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

Para implementar o modelo, siga as instruções para [O Gestor de Recursos Azure][lnk-deploy].

> [!IMPORTANT]
> Se não houver regras de IP e rede virtual, todo o tráfego flui para o espaço de nomes, mesmo que você desempate o `defaultAction` `deny` .  O espaço de nomes pode ser acedido através da internet pública (utilizando a chave de acesso). Especifique pelo menos uma regra de IP ou rede virtual para o espaço de nomes para permitir o tráfego apenas a partir dos endereços IP especificados ou sub-rede de uma rede virtual.  

## <a name="next-steps"></a>Passos seguintes

Para limitar o acesso aos Centros de Eventos às redes virtuais Azure, consulte o seguinte link:

- [Pontos finais de serviço de rede virtual para centros de eventos][lnk-vnet]

<!-- Links -->

[express-route]:  ../expressroute/expressroute-faqs.md#supported-services
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: event-hubs-service-endpoints.md
