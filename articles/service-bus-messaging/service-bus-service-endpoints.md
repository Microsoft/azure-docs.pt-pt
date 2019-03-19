---
title: Pontos finais de serviço de rede virtual e regras para o Azure Service Bus | Documentos da Microsoft
description: Adicione um ponto de extremidade do serviço de servicebus a uma rede virtual.
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: aschhab
ms.openlocfilehash: 0801469d586e6f2d6514927cdc7b894900a3aa35
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57843579"
---
# <a name="use-virtual-network-service-endpoints-with-azure-service-bus"></a>Utilizar pontos finais de serviço de rede Virtual com o Azure Service Bus

A integração do Service Bus com [pontos finais de serviço de rede Virtual (VNet)] [ vnet-sep] permite o acesso seguro às capacidades de mensagens de cargas de trabalho como as máquinas virtuais que estão vinculadas às redes virtuais , com o caminho do tráfego de rede que estão protegido em ambas as extremidades.

Depois de configurado para ser associada a pelo menos um ponto final do serviço de sub-rede virtual de rede, o espaço de nomes do Service Bus respectivo já não irá aceitar o tráfego de qualquer lugar, mas autorizado da rede ou redes virtuais. Da perspectiva de rede virtual, um espaço de nomes do Service Bus de ligação para um ponto de extremidade de serviço configura um túnel de rede isolado da sub-rede da rede virtual para o serviço de mensagens.

O resultado é uma relação isolada e privada entre as cargas de trabalho ligada à sub-rede e o namespace respectivo do Service Bus, apesar do endereço de rede observable do sistema de mensagens serviço ponto final que está a ser num intervalo IP público.

>[!WARNING]
> Implementar a integração de redes virtuais pode impedir que outros serviços do Azure a interagir com o Service Bus.
>
> Confiável Microsoft serviços não são suportados quando as redes virtuais são implementadas.
>
> Cenários comuns do Azure que não funcionam com redes virtuais (tenha em atenção que a lista está **não** exaustiva)-
> - Azure Monitor
> - Azure Stream Analytics
> - Integração com o Azure Event Grid
> - Encaminha o Hub IoT do Azure
> - O Azure IoT Device Explorer
> - Azure Data Explorer
>
> O abaixo Microsoft serviços são necessários para estar numa rede virtual
> - Serviço de Aplicações do Azure
> - Funções do Azure

> [!IMPORTANT]
> Redes virtuais são suportadas apenas num [escalão Premium](service-bus-premium-messaging.md) espaços de nomes do Service Bus.

## <a name="enable-service-endpoints-with-service-bus"></a>Ativar pontos finais de serviço com o Service Bus

Uma consideração importante ao utilizar pontos finais de serviço de VNet com o Service Bus é que não devem ser habilitadas estes pontos finais em aplicações que misturam namespaces de barramento de serviço de escalão Standard e Premium. Uma vez que o escalão Standard não suporta a VNets, o ponto final está limitado a apenas o namespaces de escalão Premium.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Cenários de segurança avançada ativados pela integração de VNet 

Soluções que requerem uma segurança forte e compartimentalizada e, em que sub-redes da rede virtual fornecem a segmentação de fornecida entre os serviços compartmentalized, geralmente ainda precisam de caminhos de comunicação entre serviços que residem em compartimentos desses.

Qualquer rotas IP imediata entre compartimentos, incluindo aquelas com HTTPS por TCP/IP, carrega o risco de exploração de vulnerabilidades da camada de rede em segurança. Serviços de mensagens fornecem caminhos de comunicação completamente isolado, onde as mensagens até mesmo são escritas no disco à medida que eles fazem a transição entre partes. Cargas de trabalho em duas redes virtuais diferentes que estão ambos vinculadas à mesma instância do Service Bus podem comunicar com eficiência e fiável através de mensagens, enquanto a integridade de limite de isolamento de rede correspondentes é preservada.
 
Isso significa que a segurança confidencial soluções na cloud não ganhará acesso apenas às capacidades do Azure líder da indústria fiáveis e escaláveis assíncronas mensagens, mas agora podem utilizar mensagens para criar caminhos de comunicação entre a solução segura compartments que são inerentemente mais seguro do que o que é conseguido com qualquer modo de comunicação de ponto-a-ponto, incluindo o HTTPS e outros protocolos de socket protegida por TLS.

## <a name="binding-service-bus-to-virtual-networks"></a>Ligação de Service Bus para redes virtuais

*Regras de rede virtual* são o recurso de segurança de firewall que controla se o seu servidor de Azure Service Bus aceita ligações a partir de uma sub-rede de rede virtual específico.

Um espaço de nomes do Service Bus de enlace a uma rede virtual é um processo de dois passos. Tem primeiro de criar uma **ponto final de serviço de rede Virtual** numa sub-rede de rede Virtual e ative-o para "Servicebus", como explicado na [descrição geral do ponto final de serviço] [ vnet-sep]. Depois de adicionar o ponto final de serviço, vincular o espaço de nomes do Service Bus ao mesmo com um *regra de rede virtual*.

A regra de rede virtual é uma associação do espaço de nomes do Service Bus com uma sub-rede de rede virtual. Embora exista a regra, todas as cargas de trabalho ligadas à sub-rede são concedidas acesso ao espaço de nomes do Service Bus. Service Bus propriamente dito nunca estabelece as ligações de saída, não precisa de obter acesso e é, portanto, nunca concedido acesso à sua sub-rede, permitindo que esta regra.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Criar uma regra de rede virtual com modelos Azure Resource Manager

O modelo do Resource Manager seguinte permite adicionar uma regra de rede virtual para um espaço de nomes do barramento de serviço existente.

Parâmetros do modelo:

* **namespaceName**: Espaço de nomes do Service Bus.
* **virtualNetworkingSubnetId**: Caminho totalmente qualificado do Resource Manager para a sub-rede de rede virtual; Por exemplo, `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` para a sub-rede de predefinição de uma rede virtual.

> [!NOTE]
> Embora não haja nenhuma regra de negação possível, o modelo Azure Resource Manager, tem a ação padrão definida como **"Permitir"** que não restringem as ligações.
> Ao fazer as regras de rede Virtual ou Firewalls, devemos alterar o ***"defaultAction"***
> 
> de
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

Para implementar o modelo, siga as instruções para [do Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre as redes virtuais, consulte as seguintes ligações:

- [Pontos finais de serviço de rede virtual do Azure][vnet-sep]
- [Filtragem de IP do Service Bus do Azure][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: service-bus-ip-filtering.md
