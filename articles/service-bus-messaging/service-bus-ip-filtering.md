---
title: Configure as regras de firewall IP para o Azure Service Bus
description: Como utilizar as Regras de Firewall para permitir ligações de endereços IP específicos para o Azure Service Bus.
ms.topic: article
ms.date: 02/12/2021
ms.openlocfilehash: 11a17575e65bc8878819767804d7f69f3d590ad3
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516554"
---
# <a name="allow-access-to-azure-service-bus-namespace-from-specific-ip-addresses-or-ranges"></a>Permitir o acesso ao espaço de nomes do Azure Service Bus a partir de endereços ou intervalos IP específicos
Por predefinição, os espaços de nome do Service Bus estão acessíveis a partir da Internet desde que o pedido venha com autenticação e autorização válidas. Com a firewall IP, pode restringi-lo ainda mais a um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (Classless Inter-Domain Encaminhamento).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Esta funcionalidade é útil em cenários em que o Azure Service Bus só deve ser acessível a partir de determinados sites conhecidos. As regras de firewall permitem-lhe configurar regras para aceitar tráfego originário de endereços IPv4 específicos. Por exemplo, se utilizar o Service Bus com [a Azure Express Route,][express-route]pode criar uma regra de **firewall** para permitir o tráfego a partir apenas dos endereços IP da sua infraestrutura no local ou endereços de um gateway NAT corporativo. 

> [!IMPORTANT]
> Firewalls e Redes Virtuais são suportados apenas no nível **premium** de Service Bus. Se o upgrade para o nível **principal** não for uma opção, recomendamos que mantenha o token de Assinatura de Acesso Partilhado (SAS) seguro e partilhe apenas com utilizadores autorizados. Para obter informações sobre a autenticação sas, consulte [autenticação e autorização.](service-bus-authentication-and-authorization.md#shared-access-signature)

## <a name="ip-firewall-rules"></a>Regras de firewall IP
As regras de firewall IP são aplicadas ao nível do espaço de nomes do Service Bus. Por isso, as regras aplicam-se a todas as ligações dos clientes utilizando qualquer protocolo suportado. Qualquer tentativa de ligação a partir de um endereço IP que não corresponda a uma regra IP permitida no espaço de nomes do Service Bus é rejeitada como não autorizada. A resposta não menciona a regra do IP. As regras do filtro IP são aplicadas por ordem, e a primeira regra que corresponde ao endereço IP determina a ação de aceitação ou rejeição.

>[!WARNING]
> Implementar regras de Firewall pode impedir que outros serviços Azure interajam com o Service Bus. Como exceção, pode permitir o acesso aos recursos do Service Bus a partir de certos serviços fidedignos, mesmo quando a filtragem IP está ativada. Para obter uma lista de serviços fidedignos, consulte [serviços Fidedignos.](#trusted-microsoft-services) 
>
> Os seguintes serviços da Microsoft são obrigados a estar numa rede virtual
> - Serviço de Aplicações do Azure
> - Funções do Azure

## <a name="use-azure-portal"></a>Utilizar o portal do Azure
Esta secção mostra-lhe como usar o portal Azure para criar regras de firewall IP para um espaço de nomes de Service Bus. 

1. Navegue para o seu espaço de nomes de **ônibus de serviço** no portal [Azure.](https://portal.azure.com)
2. No menu esquerdo, selecione a opção **de rede** em **Definições**.  

    > [!NOTE]
    > Você vê o **separador Networking** apenas para espaços de nome **premium.**  
    
    >[!WARNING]
    > Se selecionar a opção **redes selecionadas** e não adicionar pelo menos uma regra de firewall IP ou uma rede virtual nesta página, o espaço de nome pode ser acedido através da internet pública (utilizando a chave de acesso).

    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="Página de rede - padrão" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::
    
    Se selecionar a opção **Todas as redes,** o seu espaço de nomes service bus aceita ligações a partir de qualquer endereço IP. Esta predefinição é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0. 

    ![Screenshot da página de networking do portal Azure. A opção de permitir o acesso a partir de todas as redes é selecionada no separador Firewalls e redes virtuais.](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. Para permitir o acesso a partir de apenas um endereço IP especificado, selecione a opção **redes Selecionadas** se ainda não estiver selecionada. Na secção **Firewall,** siga estes passos:
    1. Selecione Adicionar a opção **de endereço IP** do seu cliente para dar ao seu cliente atual IP o acesso ao espaço de nome. 
    2. Para **o intervalo de endereços**, insira um endereço IPv4 específico ou um intervalo de endereço IPv4 na notação CIDR. 
    3. Especificar se pretende **permitir que serviços de confiança da Microsoft contornem esta firewall.** 

        > [!WARNING]
        > Se escolher a opção **redes Selecionadas** e não especificar um endereço IP ou intervalo de endereços, o serviço permitirá o tráfego de todas as redes. 

        ![Screenshot da página de networking do portal Azure. A opção de permitir o acesso a partir de redes selecionadas é selecionada e a secção Firewall é realçada.](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. **Selecione Guarde** na barra de ferramentas para guardar as definições. Aguarde alguns minutos para que a confirmação apareça nas notificações do portal.

    > [!NOTE]
    > Para restringir o acesso a redes virtuais específicas, consulte [permitir o acesso a redes específicas.](service-bus-service-endpoints.md)

[!INCLUDE [service-bus-trusted-services](../../includes/service-bus-trusted-services.md)]

## <a name="use-resource-manager-template"></a>Utilizar o modelo do Resource Manager
Esta secção tem uma amostra do modelo Azure Resource Manager que adiciona uma rede virtual e uma regra de firewall a um espaço de nomes de Service Bus existente.

**ipMask** é um único endereço IPv4 ou um bloco de endereços IP na notação CIDR. Por exemplo, na notação CIDR 70.37.104.0/24 representa os 256 endereços IPv4 de 70.37.104.0 a 70.37.104.255, com 24 indicando o número de bits prefixos significativos para a gama.

Ao adicionar regras de rede virtual ou firewalls, desa um valor de `defaultAction` `Deny` .


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
        "type": "Microsoft.ServiceBus/namespaces/networkrulesets",
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

Para implementar o modelo, siga as instruções para [O Gestor de Recursos Azure][lnk-deploy].

> [!IMPORTANT]
> Se não houver regras de IP e rede virtual, todo o tráfego flui para o espaço de nomes, mesmo que você desempate o `defaultAction` `deny` . O espaço de nomes pode ser acedido através da internet pública (utilizando a chave de acesso). Especifique pelo menos uma regra de IP ou rede virtual para o espaço de nomes para permitir o tráfego apenas a partir dos endereços IP especificados ou sub-rede de uma rede virtual.  


## <a name="next-steps"></a>Passos seguintes

Para limitar o acesso às redes virtuais service bus para Azure, consulte o seguinte link:

- [Pontos finais do serviço de rede virtual para ônibus de serviço][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  ../expressroute/expressroute-faqs.md#supported-services