---
title: Virtual rede pontos finais de serviço - Event Hubs do Azure | Documentos da Microsoft
description: Este artigo fornece informações sobre como adicionar um ponto de extremidade do serviço de eventhub a uma rede virtual.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 03/12/2019
ms.author: shvija
ms.openlocfilehash: 7b5a62f81238d1ae2b627c395613066350b36efe
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57887600"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Utilizar pontos finais de serviço de rede Virtual com o Event Hubs do Azure

A integração do Hubs de eventos com [pontos finais de serviço de rede Virtual (VNet)] [ vnet-sep] permite o acesso seguro às capacidades de mensagens de cargas de trabalho como as máquinas virtuais que estão vinculadas a virtual redes, com o caminho do tráfego de rede que estão protegidas em ambas as extremidades.

Uma vez configurado, vinculado a pelo menos um ponto final do serviço de sub-rede virtual de rede, o respetivo dos Hubs de eventos espaço de nomes já não aceita tráfego de qualquer lugar, mas autorizado sub-redes nas redes virtuais. Da perspectiva de rede virtual, ligando um espaço de nomes de Hubs de eventos a um ponto de extremidade de serviço configura um túnel de rede isolado da sub-rede da rede virtual para o serviço de mensagens. 

O resultado é uma relação isolada e privada entre as cargas de trabalho ligada à sub-rede e o namespace respectivo do Hubs de eventos, apesar do endereço de rede observable do sistema de mensagens serviço ponto final que está a ser num intervalo IP público. Existe uma exceção a este comportamento. Ativar um ponto de final de serviço, por predefinição, ativa a regra de denyall na firewall do IP associado com a rede virtual. Pode adicionar endereços IP específicos na firewall do IP para permitir o acesso ao ponto final público do Hub de eventos. 


>[!WARNING]
> Implementar a integração de redes virtuais pode impedir que outros serviços do Azure a interagir com os Hubs de eventos.
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
> - Aplicações Web do Azure
> - Funções do Azure

> [!IMPORTANT]
> Redes virtuais são suportadas no **padrão** e **dedicado** escalões de Hubs de eventos. Não é suportada no escalão básico.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Cenários de segurança avançada ativados pela integração de VNet 

Soluções que requerem uma segurança forte e compartimentalizada e, em que sub-redes da rede virtual fornecem a segmentação de fornecida entre os serviços compartmentalized, ainda precisam de caminhos de comunicação entre serviços que residem em compartimentos desses.

Qualquer rotas IP imediata entre compartimentos, incluindo aquelas com HTTPS por TCP/IP, carrega o risco de exploração de vulnerabilidades da camada de rede em segurança. Serviços de mensagens fornecem caminhos de comunicação completamente isolado, onde as mensagens até mesmo são escritas no disco à medida que eles fazem a transição entre partes. Cargas de trabalho em duas redes virtuais diferentes que estão ambos vinculadas para a mesma instância de Hubs de eventos podem comunicar com eficiência e fiável através de mensagens, enquanto a integridade de limite de isolamento de rede correspondentes é preservada.
 
Isso significa que a segurança confidencial soluções na cloud não ganhará acesso apenas às capacidades do Azure líder da indústria fiáveis e escaláveis assíncronas mensagens, mas agora podem utilizar mensagens para criar caminhos de comunicação entre a solução segura compartments que são inerentemente mais seguro do que o que é conseguido com qualquer modo de comunicação de ponto-a-ponto, incluindo o HTTPS e outros protocolos de socket protegida por TLS.

## <a name="bind-event-hubs-to-virtual-networks"></a>Ligar Hubs de eventos para redes virtuais

*Regras de rede virtual* são o recurso de segurança de firewall que controla se o seu espaço de nomes de Hubs de eventos do Azure aceita ligações a partir de uma sub-rede de rede virtual específico.

Um espaço de nomes de Hubs de eventos de enlace a uma rede virtual é um processo de dois passos. Tem primeiro de criar uma **ponto final de serviço de rede Virtual** numa sub-rede de rede Virtual e enable-lo "Eventhub", como explicado no [descrição geral do ponto final de serviço] [ vnet-sep]. Depois de adicionar o ponto final de serviço, vincular o espaço de nomes de Hubs de eventos ao mesmo com um *regra de rede virtual*.

A regra de rede virtual é uma associação do espaço de nomes dos Hubs de eventos com uma sub-rede de rede virtual. Embora exista a regra, todas as cargas de trabalho ligadas à sub-rede são concedidas acesso ao espaço de nomes dos Hubs de eventos. Os Hubs de eventos em si nunca estabelece as ligações de saída, não precisa de obter acesso e é, portanto, nunca concedida acesso à sua sub-rede, permitindo que esta regra.

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Criar uma regra de rede virtual com modelos Azure Resource Manager

O modelo do Resource Manager seguinte permite adicionar uma regra de rede virtual para um espaço de nomes de Hubs de eventos existente.

Parâmetros do modelo:

* **namespaceName**: Espaço de nomes dos Hubs de eventos.
* **vnetRuleName**: Nome para a regra de rede Virtual a ser criada.
* **virtualNetworkingSubnetId**: Caminho totalmente qualificado do Resource Manager para a sub-rede de rede virtual; Por exemplo, `subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` para a sub-rede de predefinição de uma rede virtual.

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
- [Filtragem de IPs de Hubs de eventos do Azure][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: event-hubs-ip-filtering.md
