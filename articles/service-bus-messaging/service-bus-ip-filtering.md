---
title: Configure as regras de firewall IP para o Azure Service Bus
description: Como utilizar as Regras de Firewall para permitir ligações de endereços IP específicos para o Azure Service Bus.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 378f8a6331c18b2c99e3e08e83021878f7384c2b
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87418525"
---
# <a name="allow-access-to-azure-service-bus-namespace-from-specific-ip-addresses-or-ranges"></a>Permitir o acesso ao espaço de nomes do Azure Service Bus a partir de endereços ou intervalos IP específicos
Por predefinição, os espaços de nome do Service Bus estão acessíveis a partir da Internet desde que o pedido venha com autenticação e autorização válidas. Com a firewall IP, pode restringi-lo ainda mais a um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (Roteamento Inter-Domain Sem Classe).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Esta funcionalidade é útil em cenários em que o Azure Service Bus só deve ser acessível a partir de determinados sites conhecidos. As regras de firewall permitem-lhe configurar regras para aceitar tráfego originário de endereços IPv4 específicos. Por exemplo, se utilizar o Service Bus com [a Azure Express Route,][express-route]pode criar uma regra de **firewall** para permitir o tráfego a partir apenas dos endereços IP da sua infraestrutura no local ou endereços de um gateway NAT corporativo. 

> [!IMPORTANT]
> Firewalls e Redes Virtuais são suportados apenas no nível **premium** de Service Bus. Se o upgrade para o nível **principal** não for uma opção, recomendamos que mantenha o token de Assinatura de Acesso Partilhado (SAS) seguro e partilhe apenas com utilizadores autorizados. Para obter informações sobre a autenticação sas, consulte [autenticação e autorização.](service-bus-authentication-and-authorization.md#shared-access-signature)

## <a name="ip-firewall-rules"></a>Regras de firewall IP
As regras de firewall IP são aplicadas ao nível do espaço de nomes do Service Bus. Por isso, as regras aplicam-se a todas as ligações dos clientes utilizando qualquer protocolo suportado. Qualquer tentativa de ligação a partir de um endereço IP que não corresponda a uma regra IP permitida no espaço de nomes do Service Bus é rejeitada como não autorizada. A resposta não menciona a regra do IP. As regras do filtro IP são aplicadas por ordem, e a primeira regra que corresponde ao endereço IP determina a ação de aceitação ou rejeição.

>[!WARNING]
> Implementar regras de Firewall pode impedir que outros serviços Azure interajam com o Service Bus.
>
> Os serviços fidedignos da Microsoft não são suportados quando a filtragem IP (regras de Firewall) são implementadas e serão disponibilizadas em breve.
>
> Cenários de Azure comuns que não funcionam com a filtragem IP (note que a lista **NÃO** é exaustiva) -
> - Integração com a grelha de eventos Azure
> - Rotas do Hub Azure IoT
> - Explorador de dispositivos Azure IoT
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
    
    Por predefinição, a opção **de rede Selecionada** é selecionada. Se não adicionar pelo menos uma regra de firewall IP ou uma rede virtual nesta página, o espaço de nome pode ser acedido através da internet pública (utilizando a chave de acesso).

    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="Página de rede - padrão" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::
    
    Se selecionar a opção **Todas as redes,** o seu espaço de nomes service bus aceita ligações a partir de qualquer endereço IP. Esta definição predefinida é equivalente a uma regra que aceita o intervalo de endereço IP 0.0.0.0/0. 

    ![Firewall - Todas as opções de redes selecionadas](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. Para permitir o acesso a partir de apenas um endereço IP especificado, selecione a opção **redes Selecionadas** se ainda não estiver selecionada. Na secção **Firewall,** siga estes passos:
    1. Selecione Adicionar a opção **de endereço IP** do seu cliente para dar ao seu cliente atual IP o acesso ao espaço de nome. 
    2. Para **o intervalo de endereços**, insira um endereço IPv4 específico ou um intervalo de endereço IPv4 na notação CIDR. 
    3. Especificar se pretende **permitir que serviços de confiança da Microsoft contornem esta firewall.** 

        > [!WARNING]
        > Se escolher a opção **redes Selecionadas** e não especificar um endereço IP ou intervalo de endereços, o serviço permitirá o tráfego de todas as redes. 

        ![Firewall - Todas as opções de redes selecionadas](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. **Selecione Guarde** na barra de ferramentas para guardar as definições. Aguarde alguns minutos para que a confirmação apareça nas notificações do portal.

    > [!NOTE]
    > Para restringir o acesso a redes virtuais específicas, consulte [permitir o acesso a redes específicas.](service-bus-service-endpoints.md)

## <a name="use-resource-manager-template"></a>Utilizar o modelo do Resource Manager
Esta secção tem uma amostra do modelo Azure Resource Manager que cria uma rede virtual e uma regra de firewall.


O modelo seguinte do Gestor de Recursos permite adicionar uma regra de rede virtual a um espaço de nomes de service bus existente.

Parâmetros do modelo:

- **ipMask** é um único endereço IPv4 ou um bloco de endereços IP na notação CIDR. Por exemplo, na notação CIDR 70.37.104.0/24 representa os 256 endereços IPv4 de 70.37.104.0 a 70.37.104.255, com 24 indicando o número de bits prefixos significativos para a gama.

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

Para implementar o modelo, siga as instruções para [O Gestor de Recursos Azure][lnk-deploy].

## <a name="next-steps"></a>Próximos passos

Para limitar o acesso às redes virtuais service bus para Azure, consulte o seguinte link:

- [Pontos finais do serviço de rede virtual para ônibus de serviço][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
