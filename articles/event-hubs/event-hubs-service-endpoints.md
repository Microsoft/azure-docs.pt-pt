---
title: Pontos finais de serviço de Rede Virtual - Azure Event Hubs Microsoft Docs
description: Este artigo fornece informações sobre como adicionar um ponto final de serviço Microsoft.EventHub a uma rede virtual.
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: cb0d9a9c4d5e2503e68620ec4e6386d8e05d471c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88185077"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-from-specific-virtual-networks"></a>Permitir o acesso aos espaços de nome do Azure Event Hubs a partir de redes virtuais específicas 

A integração de Centros de Eventos com [Rede Virtual (VNet) Os pontos finais][vnet-sep] de serviço permitem o acesso seguro às capacidades de mensagens a partir de cargas de trabalho, como máquinas virtuais, que estão ligadas a redes virtuais, com o caminho de tráfego da rede a ser assegurado em ambas as extremidades.

Uma vez configurado para ficar ligado a pelo menos um ponto final de serviço de sub-rede de rede virtual, o respetivo espaço de nomes do Event Hubs já não aceita tráfego de qualquer lugar, mas sub-redes autorizadas em redes virtuais. Do ponto de vista da rede virtual, a ligação de um espaço de nomes de Event Hubs a um ponto final de serviço configura um túnel de rede isolado da sub-rede de rede virtual para o serviço de mensagens. 

O resultado é uma relação privada e isolada entre as cargas de trabalho ligadas à sub-rede e o respetivo espaço de nomes De Event Hubs, apesar do endereço de rede observável do ponto final do serviço de mensagens estar numa gama pública de IP. Há uma exceção a este comportamento. Ativar um ponto final de serviço, por padrão, permite a `denyall` regra na firewall [IP](event-hubs-ip-filtering.md) associada à rede virtual. Pode adicionar endereços IP específicos na firewall IP para permitir o acesso ao ponto final público do Event Hub. 

>[!IMPORTANT]
> As redes virtuais são suportadas em níveis **padrão** e **dedicados** de Centros de Eventos. Não é suportado no nível **básico.**
>
> Ligar as regras de firewall para o seu espaço de nome Event Hubs bloqueia os pedidos de entrada por padrão, a menos que os pedidos sejam originários de um serviço que opera a partir de redes virtuais permitidas. Os pedidos que estão bloqueados incluem os de outros serviços Azure, do portal Azure, de serviços de registo e métricas, e assim por diante. 
>
> Aqui estão alguns dos serviços que não conseguem aceder aos recursos do Event Hubs quando as redes virtuais estão ativadas. Note que a lista **NÃO** é exaustiva.
>
> - Azure Stream Analytics
> - Rotas do Hub Azure IoT
> - Explorador de dispositivos Azure IoT
> - Azure Event Grid
> - Monitor Azure (Definições de diagnóstico)
>
> Como exceção, pode permitir o acesso aos recursos do Event Hubs a partir de determinados serviços fidedignos, mesmo quando as redes virtuais estão ativadas. Para obter uma lista de serviços fidedignos, consulte [serviços Fidedignos.](#trusted-microsoft-services)

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Cenários avançados de segurança habilitados pela integração do VNet 

As soluções que requerem uma segurança apertada e compartimentada, e onde as sub-redes de rede virtuais fornecem a segmentação entre os serviços compartimentados, ainda precisam de vias de comunicação entre serviços residentes nesses compartimentos.

Qualquer via IP imediata entre os compartimentos, incluindo os que transportam HTTPS sobre TCP/IP, comporta o risco de exploração de vulnerabilidades a partir da camada de rede em cima. Os serviços de mensagens fornecem caminhos de comunicação isolados, onde as mensagens são mesmo escritas para o disco à medida que fazem a transição entre as partes. Cargas de trabalho em duas redes virtuais distintas que estão ambas ligadas à mesma instância Event Hubs podem comunicar de forma eficiente e fiável através de mensagens, enquanto a respetiva integridade da fronteira de isolamento da rede é preservada.
 
Isto significa que as suas soluções de nuvem sensíveis à segurança não só têm acesso a capacidades de mensagens assíncronas fiáveis e escaláveis da indústria Azure, como podem agora utilizar mensagens para criar caminhos de comunicação entre compartimentos de solução seguros que são inerentemente mais seguros do que o que é possível com qualquer modo de comunicação peer-to-peer, incluindo HTTPS e outros protocolos de tomadas seguros.

## <a name="bind-event-hubs-to-virtual-networks"></a>Ligar centros de eventos a redes virtuais

**As regras de rede virtual** são a funcionalidade de segurança de firewall que controla se o seu espaço de nomeS Azure Event Hubs aceita ligações a partir de uma determinada sub-rede de rede virtual.

Ligar um espaço de nomes de Event Hubs a uma rede virtual é um processo em duas etapas. Primeiro, precisa de criar um **ponto final de serviço de rede virtual** na sub-rede de uma rede virtual e de o ativar para o **Microsoft.EventHub,** como explicado no artigo de [visão geral][vnet-sep] do ponto de vista do serviço. Depois de ter adicionado o ponto final do serviço, liga-lhe o espaço de nomes Do Event Hubs com uma **regra de rede virtual.**

A regra da rede virtual é uma associação do espaço de nomes do Event Hubs com uma sub-rede de rede virtual. Embora a regra exista, todas as cargas de trabalho ligadas à sub-rede têm acesso ao espaço de nomes do Event Hubs. O Próprio Event Hubs nunca estabelece ligações de saída, não precisa de ter acesso e, portanto, nunca tem acesso à sua sub-rede, permitindo esta regra.

## <a name="use-azure-portal"></a>Utilizar o portal do Azure
Esta secção mostra-lhe como usar o portal Azure para adicionar um ponto final de serviço de rede virtual. Para limitar o acesso, é necessário integrar o ponto final do serviço de rede virtual para este espaço de nomes do Event Hubs.

1. Navegue para o seu **espaço de nomes de Centros de Eventos** no [portal Azure.](https://portal.azure.com)
4. Selecione **rede em** **Definições** no menu esquerdo. Você vê o **separador Networking** apenas para espaços de nome **padrão** ou **dedicados.** 

    > [!NOTE]
    > Por predefinição, a opção **de redes Selecionada** é selecionada como mostrado na imagem seguinte. Se não especificar uma regra de firewall IP ou adicionar uma rede virtual nesta página, o espaço de nome pode ser acedido através da **internet pública** (utilizando a chave de acesso). 

    :::image type="content" source="./media/event-hubs-firewall/selected-networks.png" alt-text="Separador de redes - opção de redes selecionada" lightbox="./media/event-hubs-firewall/selected-networks.png":::    

    Se selecionar a opção **Todas as redes,** o centro de eventos aceita ligações a partir de qualquer endereço IP (utilizando a chave de acesso). Esta definição é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0. 

    ![Firewall - Todas as opções de redes selecionadas](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Para restringir o acesso a redes específicas, selecione a opção **Redes Selecionadas** no topo da página se ainda não estiver selecionada.
2. Na secção **Rede Virtual** da página, selecione **+Adicionar a rede virtual existente****. Selecione **+ Crie uma nova rede virtual** se quiser criar um novo VNet. 

    ![adicionar rede virtual existente](./media/event-hubs-tutorial-vnet-and-firewalls/add-vnet-menu.png)
3. Selecione a rede virtual a partir da lista de redes virtuais e, em seguida, escolha a **sub-rede**. Tem de ativar o ponto final de serviço antes de adicionar a rede virtual à lista. Se o ponto final de serviço não estiver ativado, o portal irá solicitar-lhe para o ativar.
   
   ![selecionar sub-redes](./media/event-hubs-tutorial-vnet-and-firewalls/select-subnet.png)

4. Deverá ver a seguinte mensagem bem sucedida após o ponto final de serviço da sub-rede estar ativado para **o Microsoft.EventHub**. **Selecione Adicionar** na parte inferior da página para adicionar a rede. 

    ![selecionar sub-rede e ativar o ponto final](./media/event-hubs-tutorial-vnet-and-firewalls/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Se não conseguir ativar o ponto final do serviço, poderá ignorar o ponto final de serviço de rede virtual em falta utilizando o modelo de Gestor de Recursos. Esta funcionalidade não se encontra disponível no portal.
5. Especificar se pretende **permitir que serviços de confiança da Microsoft contornem esta firewall.** Consulte [os serviços da Microsoft fidedignos](#trusted-microsoft-services) para obter mais detalhes. 
6. **Selecione Guarde** na barra de ferramentas para guardar as definições. Aguarde alguns minutos para que a confirmação apareça nas notificações do portal.

    ![Salvar rede](./media/event-hubs-tutorial-vnet-and-firewalls/save-vnet.png)

    > [!NOTE]
    > Para restringir o acesso a endereços ou intervalos IP específicos, consulte [Permitir o acesso a partir de endereços ou intervalos IP específicos](event-hubs-ip-filtering.md).

[!INCLUDE [event-hubs-trusted-services](../../includes/event-hubs-trusted-services.md)]

## <a name="use-resource-manager-template"></a>Utilizar o modelo do Resource Manager

O modelo seguinte do Gestor de Recursos permite adicionar uma regra de rede virtual a um espaço de nomes de Centros de Eventos existente.

Parâmetros do modelo:

* `namespaceName`: Espaço de nomes do Event Hubs.
* `vnetRuleName`: Nome da regra da Rede Virtual a criar.
* `virtualNetworkingSubnetId`: Caminho de Gestor de Recursos totalmente qualificado para a sub-rede de rede virtual; por exemplo, `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` para a sub-rede predefinida de uma rede virtual.

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
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
        }
      },
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
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
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
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
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.EventHub"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
          "trustedServiceAccessEnabled": false,
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Para implementar o modelo, siga as instruções para [O Gestor de Recursos Azure][lnk-deploy].

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre redes virtuais, consulte os seguintes links:

- [Pontos finais de serviço de rede virtual Azure][vnet-sep]
- [Filtragem IP dos Hubs de Eventos Azure][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: event-hubs-ip-filtering.md
