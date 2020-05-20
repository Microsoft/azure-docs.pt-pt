---
title: Configure regras de firewall IP para ônibus de serviço Azure
description: Como utilizar as Regras de Firewall para permitir ligações de endereços IP específicos para o Ônibus de Serviço Azure.
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 05/14/2020
ms.author: aschhab
ms.openlocfilehash: fdd3540248c5210e2f6fc47f439641c007a793d8
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647816"
---
# <a name="configure-ip-firewall-rules-for-azure-service-bus"></a>Configure regras de firewall IP para ônibus de serviço Azure
Por predefinição, os espaços de nome service Bus são acessíveis a partir da internet desde que o pedido venha com autenticação e autorização válidas. Com firewall IP, pode restringi-lo ainda mais a apenas um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Esta funcionalidade é útil em cenários em que o Azure Service Bus só deve estar acessível a partir de certos sites bem conhecidos. As regras de firewall permitem-lhe configurar regras para aceitar o tráfego originário de endereços IPv4 específicos. Por exemplo, se utilizar o Service Bus com a [Rota Expresso Azure,][express-route]pode criar uma regra de **firewall** para permitir o tráfego a partir apenas dos seus endereços IP de infraestrutura no local ou endereços de um gateway NAT corporativo. 

> [!IMPORTANT]
> Firewalls e Redes Virtuais são suportados apenas no nível **premium** de Ônibus de Serviço. Se a atualização para o nível **principal** não for uma opção, recomendamos que mantenha o token Signature de Acesso Partilhado (SAS) seguro e partilhe apenas com utilizadores autorizados. Para obter informações sobre a autenticação SAS, consulte [Autenticação e autorização.](service-bus-authentication-and-authorization.md#shared-access-signature)

## <a name="ip-firewall-rules"></a>Regras de firewall IP
As regras de firewall IP são aplicadas ao nível do espaço de nome do Ônibus de serviço. Por isso, as regras aplicam-se a todas as ligações dos clientes que utilizam qualquer protocolo suportado. Qualquer tentativa de ligação a partir de um endereço IP que não corresponda a uma regra ip permitida no espaço de nome do Ônibus de serviço é rejeitada como não autorizada. A resposta não menciona a regra do PI. As regras do filtro IP são aplicadas por ordem, e a primeira regra que corresponde ao endereço IP determina a aceitação ou rejeita a ação.

>[!WARNING]
> A implementação das regras da Firewall pode impedir que outros serviços Azure interajam com o Service Bus.
>
> Os serviços fidedignos da Microsoft não são suportados quando a filtragem ip (regras de Firewall) for implementada, e será disponibilizado em breve.
>
> Cenários Azure comuns que não funcionam com filtragem IP (note que a lista **NÃO** é exaustiva) -
> - Integração com a Grelha de Eventos Azure
> - Rotas do Hub Azure IoT
> - Explorador de dispositivos Azure IoT
>
> Os seguintes serviços da Microsoft são obrigados a estar numa rede virtual
> - Serviço de Aplicações do Azure
> - Funções do Azure

## <a name="use-azure-portal"></a>Utilizar o portal do Azure
Esta secção mostra-lhe como usar o portal Azure para criar regras de firewall IP para um espaço de nome de ônibus de serviço. 

1. Navegue para o seu espaço de **nome service Bus** no portal [Azure](https://portal.azure.com).
2. No menu esquerdo, selecione a opção **Networking.** Por predefinição, a opção **Todas as redes** é selecionada. O seu espaço de nome do Ônibus de serviço aceita ligações de qualquer endereço IP. Esta definição predefinida equivale a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0. 

    ![Firewall - Todas as redes selecionadas](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. Selecione a opção **redes Selecionadas** no topo da página. Na secção **Firewall,** siga estes passos:
    1. Selecione Adicionar a opção de **endereço IP do seu cliente** para dar ao seu cliente atual IP o acesso ao espaço de nome. 
    2. Para o intervalo de **endereços,** introduza um endereço IPv4 específico ou um conjunto de endereços IPv4 na notação CIDR. 
    3. Especifique se pretende **permitir que serviços microsoft fidedignos contornem esta firewall**. 

        > [!WARNING]
        > Se escolher a opção de **redes Selecionadas** e não especificar um endereço IP ou intervalo de endereços, o serviço permitirá o tráfego de todas as redes. 

        ![Firewall - Todas as redes selecionadas](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. Selecione **Guardar** na barra de ferramentas para guardar as definições. Aguarde alguns minutos para que a confirmação apareça nas notificações do portal.

## <a name="use-resource-manager-template"></a>Utilizar o modelo do Resource Manager
Esta secção tem um modelo de Gestor de Recursos Azure que cria uma rede virtual e uma regra de firewall.


O seguinte modelo de Gestor de Recursos permite adicionar uma regra de rede virtual a um espaço de nome de ônibus de serviço existente.

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

Para limitar o acesso às redes virtuais De Serviço bus para Azure, consulte o seguinte link:

- [Pontos finais do serviço de rede virtual para ônibus de serviço][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
