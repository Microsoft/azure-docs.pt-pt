---
title: Pontos de extremidade de serviço de rede virtual-barramento de serviço do Azure
description: Adicione um ponto de extremidade de serviço Microsoft. ServiceBus a uma rede virtual.
services: service-bus
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.date: 10/22/2018
ms.author: aschhab
ms.openlocfilehash: f32a67dc6d3b3f869afaa532403c05b218588552
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72786388"
---
# <a name="use-virtual-network-service-endpoints-with-azure-service-bus"></a>Usar pontos de extremidade de serviço de rede virtual com o barramento de serviço do Azure

A integração do barramento de serviço com [pontos de extremidade de serviço de rede virtual (VNet)][vnet-sep] permite o acesso seguro a recursos de mensagens de cargas de trabalho como máquinas virtuais associadas a redes virtuais, com o caminho de tráfego de rede protegido em ambos lados.

Uma vez configurado para ser associado a pelo menos um ponto de extremidade de serviço de sub-rede de rede virtual, o respectivo namespace do barramento de serviço não aceitará mais tráfego de qualquer lugar, mas redes virtuais autorizadas. Da perspectiva da rede virtual, a associação de um namespace do barramento de serviço a um ponto de extremidade de serviço configura um túnel de rede isolado da sub-rede da rede virtual para o serviço de mensagens.

O resultado é uma relação privada e isolada entre as cargas de trabalho vinculadas à sub-rede e o respectivo namespace do barramento de serviço, apesar do endereço de rede observável do ponto de extremidade do serviço de mensagens estar em um intervalo de IP público.

>[!WARNING]
> A implementação da integração de redes virtuais pode impedir que outros serviços do Azure interajam com o barramento de serviço.
>
> Os serviços confiáveis da Microsoft não têm suporte quando as redes virtuais são implementadas.
>
> Cenários comuns do Azure que não funcionam com redes virtuais (Observe que a lista **não** é exaustiva) –
> - Monitor do Azure
> - Azure Stream Analytics
> - Integração com a grade de eventos do Azure
> - Rotas do Hub IoT do Azure
> - Device Explorer de IoT do Azure
> - Explorador de Dados do Azure
>
> Os serviços da Microsoft a seguir devem estar em uma rede virtual
> - Serviço de Aplicações do Azure
> - Funções do Azure

> [!IMPORTANT]
> As redes virtuais têm suporte apenas em namespaces de barramento de serviço da [camada Premium](service-bus-premium-messaging.md) .

## <a name="enable-service-endpoints-with-service-bus"></a>Habilitar pontos de extremidade de serviço com o barramento de serviço

Uma consideração importante ao usar pontos de extremidade de serviço de VNet com o barramento de serviço é que você não deve habilitar esses pontos de extremidade em aplicativos que combinam os namespaces do barramento de serviço da camada Standard e Premium. Como a camada Standard não dá suporte a VNets, o ponto de extremidade é restrito apenas aos namespaces da camada Premium.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Cenários de segurança avançada habilitados pela integração de VNet 

Soluções que exigem segurança rígida e segmentada e onde as sub-redes da rede virtual fornecem a segmentação entre os serviços em compartimentalização, geralmente ainda precisam de caminhos de comunicação entre os serviços que residem nesses compartimentos.

Qualquer rota de IP imediata entre os compartimentos, incluindo aqueles que realizam HTTPS sobre TCP/IP, traz o risco de exploração de vulnerabilidades da camada de rede em funcionamento. Os serviços de mensagens fornecem caminhos de comunicação completamente isolados, em que as mensagens são gravadas no disco à medida que fazem a transição entre as partes. As cargas de trabalho em duas redes virtuais distintas associadas à mesma instância do barramento de serviço podem se comunicar de forma eficiente e confiável por meio de mensagens, enquanto a respectiva integridade de limite de isolamento de rede é preservada.
 
Isso significa que suas soluções de nuvem sensíveis à segurança não só têm acesso aos recursos de mensagens assíncronas confiáveis e escalonáveis líderes do setor do Azure, mas agora podem usar o sistema de mensagens para criar caminhos de comunicação entre compartimentos de solução seguros que são inerentemente mais seguras do que o que é atingível com qualquer modo de comunicação ponto a ponto, incluindo HTTPS e outros protocolos de soquete protegidos por TLS.

## <a name="binding-service-bus-to-virtual-networks"></a>Associando o barramento de serviço a redes virtuais

*As regras de rede virtual* são o recurso de segurança de firewall que controla se o servidor do barramento de serviço do Azure aceita conexões de uma sub-rede de rede virtual específica.

A associação de um namespace do barramento de serviço a uma rede virtual é um processo de duas etapas. Primeiro, você precisa criar um **ponto de extremidade de serviço de rede virtual** em uma sub-rede de rede virtual e habilitá-la para "Microsoft. ServiceBus", conforme explicado na [visão geral do ponto de extremidade do serviço][vnet-sep]. Depois de adicionar o ponto de extremidade de serviço, você associa o namespace do barramento de serviço a ele com uma *regra de rede virtual*.

A regra de rede virtual é uma associação do namespace do barramento de serviço com uma sub-rede de rede virtual. Embora a regra exista, todas as cargas de trabalho vinculadas à sub-rede recebem acesso ao namespace do barramento de serviço. O próprio barramento de serviço nunca estabelece conexões de saída, não precisa ter acesso e, portanto, nunca concede acesso à sua sub-rede habilitando essa regra.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Criando uma regra de rede virtual com modelos de Azure Resource Manager

O modelo do Resource Manager a seguir permite adicionar uma regra de rede virtual a um namespace do barramento de serviço existente.

Parâmetros do modelo:

* **NamespaceName**: namespace do barramento de serviço.
* **virtualNetworkingSubnetId**: caminho do Gerenciador de recursos totalmente qualificado para a sub-rede da rede virtual; por exemplo, `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` para a sub-rede padrão de uma rede virtual.

> [!NOTE]
> Embora não haja nenhuma regra de negação possível, o modelo de Azure Resource Manager tem a ação padrão definida como **"permitir"** , que não restringe as conexões.
> Ao tornar as regras de rede virtual ou firewalls, devemos alterar o ***"DefaultAction"***
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
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Para implantar o modelo, siga as instruções para [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre redes virtuais, consulte os links a seguir:

- [Pontos de extremidade de serviço de rede virtual do Azure][vnet-sep]
- [Filtragem de IP do barramento de serviço do Azure][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: service-bus-ip-filtering.md
