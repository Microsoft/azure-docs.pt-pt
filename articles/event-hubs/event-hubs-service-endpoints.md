---
title: Pontos finais do serviço de Rede Virtual - Hubs de Eventos Azure / Microsoft Docs
description: Este artigo fornece informações sobre como adicionar um ponto final do serviço Microsoft.EventHub a uma rede virtual.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 11/26/2019
ms.author: shvija
ms.openlocfilehash: abd7940551f7a8182364475b0cf50b60afb5e1b7
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313799"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Utilize pontos finais de serviço de rede virtual com hubs de eventos Azure

A integração de Hubs de Eventos com Pontos finais de Serviço de [Rede Virtual (VNet)][vnet-sep] permite o acesso seguro a capacidades de mensagens a partir de cargas de trabalho como máquinas virtuais que estão ligadas a redes virtuais, com a trajetória de tráfego da rede a ser protegida em ambas as extremidades.

Uma vez configurado para vincular-se a pelo menos um ponto final de serviço de rede virtual, o respetivo espaço de nome sem nome do Event Hubs já não aceita tráfego de qualquer lugar, mas autorizado subredes em redes virtuais. Do ponto de vista da rede virtual, vincular um espaço de nome do Event Hubs a um ponto final de serviço configura um túnel de rede isolado da subnet da rede virtual para o serviço de mensagens. 

O resultado é uma relação privada e isolada entre as cargas de trabalho ligadas à subnet e o respetivo espaço de nome sem nome do Event Hubs, apesar do endereço de rede observável do ponto final do serviço de mensagens estar numa gama de IP pública. Há uma exceção a este comportamento. Ativar um ponto final de serviço, `denyall` por defeito, permite a regra na [firewall IP](event-hubs-ip-filtering.md) associada à rede virtual. Pode adicionar endereços IP específicos na firewall IP para permitir o acesso ao ponto final do Event Hub. 

>[!WARNING]
> A implementação da integração das Redes Virtuais pode impedir que outros serviços Azure interajam com os Centros de Eventos.
>
> Os serviços fidedignos da Microsoft não são suportados quando as Redes Virtuais são implementadas.
>
> Cenários Comuns Azure que não funcionam com Redes Virtuais (note que a lista **NÃO** é exaustiva) -
> - Azure Stream Analytics
> - Integração com a Grelha de Eventos Azure
> - Rotas do Hub Azure IoT
> - Explorador de dispositivos Azure IoT
>
> Os seguintes serviços da Microsoft são obrigados a estar numa rede virtual
> - Aplicações Web do Azure
> - Funções do Azure


> [!IMPORTANT]
> As redes virtuais são suportadas em níveis **standard** e **dedicados** de Centros de Eventos. Não é suportado no nível **básico.**

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Cenários de segurança avançados possibilitados pela integração vnet 

As soluções que requerem segurança apertada e compartimentada, e onde as redes virtuais fornecem a segmentação entre os serviços compartimentados, ainda precisam de caminhos de comunicação entre os serviços residentes nesses compartimentos.

Qualquer rota IP imediata entre os compartimentos, incluindo os que transportam HTTPS sobre TCP/IP, acarreta o risco de exploração de vulnerabilidades da camada de rede para cima. Os serviços de mensagens fornecem caminhos de comunicação isolados, onde as mensagens são até escritas em disco à medida que transitam entre as partes. As cargas de trabalho em duas redes virtuais distintas que estão ambas ligadas ao mesmo caso de Hubs de Eventos podem comunicar de forma eficiente e fiável através de mensagens, enquanto a respetiva integridade de fronteira de isolamento da rede é preservada.
 
Isto significa que as suas soluções de nuvem sensíveis à segurança não só têm acesso a capacidades de mensagens assíncronas fiáveis e escaláveis, como podem agora utilizar mensagens para criar caminhos de comunicação entre compartimentos de solução segura que são inerentemente mais seguros do que o que é possível com qualquer modo de comunicação entre pares, incluindo HTTPS e outros protocolos de tomada seletivos protegidos por TLS.

## <a name="bind-event-hubs-to-virtual-networks"></a>Ligar centros de eventos a redes virtuais

**As regras** de rede virtuais são a funcionalidade de segurança da firewall que controla se o seu espaço de nome sem nome do Azure Event Hubs aceita ligações a partir de uma determinada subnet de rede virtual.

Vincular um espaço de nome de Event Hubs a uma rede virtual é um processo em duas etapas. Primeiro é necessário criar um **ponto final** de serviço de rede virtual na subnet de uma rede virtual e capacitá-lo para o **Microsoft.EventHub,** conforme explicado no artigo de visão geral do ponto final do [serviço.][vnet-sep] Uma vez adicionado o ponto final do serviço, ligue o espaço de nome do Event Hubs a ele com uma regra de **rede virtual**.

A regra da rede virtual é uma associação do espaço de nome do Event Hubs com uma subnet de rede virtual. Enquanto a regra existe, todas as cargas de trabalho ligadas à subnet têm acesso ao espaço de nome do Event Hubs. O Próprio Event Hubs nunca estabelece ligações de saída, não precisa de ter acesso, pelo que nunca é concedido acesso à sua subnet, permitindo esta regra.

## <a name="use-azure-portal"></a>Utilizar o portal do Azure
Esta secção mostra-lhe como usar o portal Azure para adicionar um ponto final de serviço de rede virtual. Para limitar o acesso, precisa de integrar o ponto final do serviço de rede virtual para este espaço de nome Sem Eventos Hubs.

1. Navegue para o seu espaço de **nome Sem Nome Do seu Evento Hubs** no [portal Azure](https://portal.azure.com).
2. No menu esquerdo, selecione a opção **Networking.** Se selecionar a opção **Todas as redes,** o hub do evento aceita ligações a partir de qualquer endereço IP. Esta definição equivale a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0. 

    ![Firewall - Todas as redes selecionadas](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Para repousar o acesso a redes específicas, selecione a opção **Redes Selecionadas** no topo da página.
2. Na secção **Rede Virtual** da página, selecione **+Adicionar rede virtual existente***. Selecione **+ Crie uma nova rede virtual** se quiser criar um novo VNet. 

    ![adicionar rede virtual existente](./media/event-hubs-tutorial-vnet-and-firewalls/add-vnet-menu.png)
3. Selecione a rede virtual a partir da lista de redes virtuais e, em seguida, escolha a **sub-rede**. Tem de ativar o ponto final do serviço antes de adicionar a rede virtual à lista. Se o ponto final do serviço não estiver ativado, o portal irá pedir-lhe para o ativar.
   
   ![selecionar sub-rede](./media/event-hubs-tutorial-vnet-and-firewalls/select-subnet.png)

4. Deve ver a seguinte mensagem de sucesso após o ponto final do serviço para a sub-rede estar ativado para **microsoft.EventHub**. Selecione **Adicionar** na parte inferior da página para adicionar a rede. 

    ![selecionar sub-rede e ativar ponto final](./media/event-hubs-tutorial-vnet-and-firewalls/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Se não conseguir ativar o ponto final do serviço, poderá ignorar o ponto final do serviço de rede virtual em falta utilizando o modelo de Gestor de Recursos. Esta funcionalidade não está disponível no portal.
6. Selecione **Guardar** na barra de ferramentas para guardar as definições. Aguarde alguns minutos para que a confirmação apareça nas notificações do portal.

    ![Salvar rede](./media/event-hubs-tutorial-vnet-and-firewalls/save-vnet.png)


## <a name="use-resource-manager-template"></a>Utilizar o modelo do Resource Manager

O seguinte modelo de Gestor de Recursos permite adicionar uma regra de rede virtual a um espaço de nome existente para os Hubs de Eventos.

Parâmetros do modelo:

* **nomenome nome**: Espaço de nome de Centros de Eventos.
* **vnetRuleName**: Nome para a regra da Rede Virtual a criar.
* **virtualNetworkingSubnetId**: Caminho de Gestor de Recursos totalmente qualificado para a subnet de rede virtual; por exemplo, `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` para a sub-rede padrão de uma rede virtual.

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

Para implementar o modelo, siga as instruções para o Gestor de [Recursos Azure][lnk-deploy].

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre redes virtuais, consulte os seguintes links:

- [Pontos finais do serviço de rede virtual Azure][vnet-sep]
- [Filtragem IP do Azure Event Hubs][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: event-hubs-ip-filtering.md
