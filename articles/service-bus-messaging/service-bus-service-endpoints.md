---
title: Configure pontos finais de serviço de rede virtual para ônibus de serviço Azure
description: Este artigo fornece informações sobre como adicionar um ponto final do serviço Microsoft.ServiceBus a uma rede virtual.
services: service-bus
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: d1766ffb579bb1a86da91ac73a396ce0d008f89e
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117629"
---
# <a name="configure-virtual-network-service-endpoints-for-azure-service-bus"></a>Configure pontos finais de serviço de rede virtual para ônibus de serviço Azure

A integração do Service Bus com [pontos finais de serviço da Rede Virtual (VNet)][vnet-sep] permite o acesso seguro a capacidades de mensagens a partir de cargas de trabalho como máquinas virtuais que estão ligadas a redes virtuais, com a trajetória de tráfego da rede a ser protegida em ambas as extremidades.

Uma vez configurado para ser ligado a pelo menos um ponto final de serviço de rede virtual, o respetivo espaço de nome do Ônibus de Serviço deixará de aceitar o tráfego de qualquer lugar, mas sim rede virtual autorizada. Do ponto de vista da rede virtual, vincular um espaço de nome service Bus a um ponto final de serviço configura um túnel de rede isolado da subnet da rede virtual para o serviço de mensagens.

O resultado é uma relação privada e isolada entre as cargas de trabalho ligadas à subnet e o respetivo espaço de nome saque de serviço, apesar do endereço observável da rede do ponto final do serviço de mensagens estar numa gama de IP pública.

>[!WARNING]
> A implementação da integração das Redes Virtuais pode impedir que outros serviços Azure interajam com o Service Bus.
>
> Os serviços fidedignos da Microsoft não são suportados quando as Redes Virtuais são implementadas.
>
> Cenários Comuns Azure que não funcionam com Redes Virtuais (note que a lista **NÃO** é exaustiva) -
> - Integração com a Grelha de Eventos Azure
> - Rotas do Hub Azure IoT
> - Explorador de dispositivos Azure IoT
>
> Os serviços abaixo da Microsoft são obrigados a estar numa rede virtual
> - Serviço de Aplicações do Azure
> - Funções do Azure

> [!IMPORTANT]
> As Redes Virtuais são suportadas apenas em espaços de nome de ônibus de [serviço de nível Premium.](service-bus-premium-messaging.md)
> 
> Ao utilizar pontos finais de serviço VNet com o Service Bus, não deve ativar estes pontos finais em aplicações que misturam espaços de nome sinuosos de ônibus de serviço standard e premium. Porque o nível Standard não suporta VNets. O ponto final é restrito apenas a espaços de nome de nível Premium.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Cenários de segurança avançados possibilitados pela integração vnet 

Soluções que requerem segurança apertada e compartimentada, e onde as redes virtuais fornecem a segmentação entre os serviços compartimentados, geralmente ainda precisam de caminhos de comunicação entre os serviços residentes nesses compartimentos.

Qualquer rota IP imediata entre os compartimentos, incluindo os que transportam HTTPS sobre TCP/IP, acarreta o risco de exploração de vulnerabilidades da camada de rede para cima. Os serviços de mensagens fornecem caminhos de comunicação completamente isolados, onde as mensagens são até escritas em disco à medida que transitam entre as partes. As cargas de trabalho em duas redes virtuais distintas que estão ambas ligadas à mesma instância de Ônibus de serviço podem comunicar de forma eficiente e fiável através de mensagens, enquanto a respetiva integridade de fronteira de isolamento da rede é preservada.
 
Isto significa que as suas soluções de nuvem sensíveis à segurança não só têm acesso a capacidades de mensagens assíncronas fiáveis e escaláveis, como podem agora utilizar mensagens para criar caminhos de comunicação entre compartimentos de solução segura que são inerentemente mais seguros do que o que é possível com qualquer modo de comunicação entre pares, incluindo HTTPS e outros protocolos de tomada seletivos protegidos por TLS.

## <a name="binding-service-bus-to-virtual-networks"></a>Ônibus de serviço de ligação para redes virtuais

*As regras* de rede virtuais são a funcionalidade de segurança da firewall que controla se o seu servidor Deônibus de Serviço Azure aceita ligações a partir de uma determinada subnet de rede virtual.

Vincular um espaço de nome de ônibus de serviço a uma rede virtual é um processo em duas etapas. Primeiro é necessário criar um **ponto final** de serviço de Rede Virtual numa subnet de Rede Virtual e capacitá-lo para a **Microsoft.ServiceBus,** conforme explicado na visão geral do [ponto final][vnet-sep]do serviço . Depois de ter adicionado o ponto final do serviço, ligue-lhe o espaço de nome do Ônibus de serviço com uma regra de **rede virtual**.

A regra da rede virtual é uma associação do espaço de nome do Service Bus com uma subnet de rede virtual. Enquanto a regra existe, todas as cargas de trabalho ligadas à subnet têm acesso ao espaço de nome do Ônibus de serviço. O próprio Service Bus nunca estabelece ligações de saída, não precisa de ter acesso, pelo que nunca é concedido acesso à sua subnet, permitindo esta regra.

## <a name="use-azure-portal"></a>Utilizar o portal do Azure
Esta secção mostra-lhe como usar o portal Azure para adicionar um ponto final de serviço de rede virtual. Para limitar o acesso, precisa de integrar o ponto final do serviço de rede virtual para este espaço de nome Sem Eventos Hubs.

1. Navegue para o seu espaço de **nome service Bus** no portal [Azure](https://portal.azure.com).
2. No menu esquerdo, selecione a opção **Networking.** Por predefinição, a opção **Todas as redes** é selecionada. O seu espaço de nome aceita ligações de qualquer endereço IP. Esta definição predefinida equivale a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0. 

    ![Firewall - Todas as redes selecionadas](./media/service-endpoints/firewall-all-networks-selected.png)
1. Selecione a opção **Redes Selecionadas** no topo da página.
2. Na secção **Rede Virtual** da página, selecione **+Adicionar rede virtual existente**. 

    ![adicionar rede virtual existente](./media/service-endpoints/add-vnet-menu.png)
3. Selecione a rede virtual a partir da lista de redes virtuais e, em seguida, escolha a **sub-rede**. Tem de ativar o ponto final do serviço antes de adicionar a rede virtual à lista. Se o ponto final do serviço não estiver ativado, o portal irá pedir-lhe para o ativar.
   
   ![selecionar sub-rede](./media/service-endpoints/select-subnet.png)

4. Deve ver a seguinte mensagem de sucesso após o ponto final do serviço para a sub-rede estar ativado para **microsoft.ServiceBus**. Selecione **Adicionar** na parte inferior da página para adicionar a rede. 

    ![selecionar sub-rede e ativar ponto final](./media/service-endpoints/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Se não conseguir ativar o ponto final do serviço, poderá ignorar o ponto final do serviço de rede virtual em falta utilizando o modelo de Gestor de Recursos. Esta funcionalidade não está disponível no portal.
6. Selecione **Guardar** na barra de ferramentas para guardar as definições. Aguarde alguns minutos para que a confirmação apareça nas notificações do portal. O botão **Guardar** deve ser desativado. 

    ![Salvar rede](./media/service-endpoints/save-vnet.png)

## <a name="use-resource-manager-template"></a>Utilizar o modelo do Resource Manager
O seguinte modelo de Gestor de Recursos permite adicionar uma regra de rede virtual a um espaço de nome de ônibus de serviço existente.

Parâmetros do modelo:

* **nomenome nome**: Service Bus namespace.
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

Modelo:

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
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
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
                    "service": "Microsoft.ServiceBus"
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
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
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
- [Filtragem IP do ônibus de serviço Azure][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: service-bus-ip-filtering.md
