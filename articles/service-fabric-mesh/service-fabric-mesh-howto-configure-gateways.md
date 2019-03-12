---
title: Configurar um Gateway para encaminhar os pedidos | Documentos da Microsoft
description: Saiba como configurar o gateway que processa o tráfego de entrada para as aplicações em execução no Service Fabric em malha.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/28/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 1c75d781c8a83b54ac9474c83388cf02b5d03e3c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57532087"
---
# <a name="configure-a-gateway-resource-to-route-requests"></a>Configurar um recurso de Gateway para encaminhar os pedidos

Um recurso de Gateway é utilizado para encaminhar o tráfego de entrada para a rede que hospeda a sua aplicação. Configure para especificar regras por meio do qual os pedidos são direcionados para serviços específicos ou pontos finais com base na estrutura do pedido. Ver [introdução ao funcionamento em rede no Service Fabric em malha](service-fabric-mesh-networks-and-gateways.md) para obter mais informações sobre redes e gateways na malha. 

Recursos de gateway tem de ser declarado como parte do seu modelo de implementação (JSON ou yaml) e que são dependentes de um recurso de rede. Este documento descreve as várias propriedades que podem ser definidas para o seu gateway e abrange uma configuração de gateway de exemplo.

## <a name="options-for-configuring-your-gateway-resource"></a>Opções de configuração do seu recurso de Gateway

Uma vez que o recurso de Gateway funciona como uma ponte entre a rede da sua aplicação e de rede a infraestrutura subjacente (o `open` rede). Só deverá precisar de configurar uma (na pré-visualização da malha, há um limite de um gateway por aplicação). A declaração para o recurso consiste em duas partes principais: as propriedades e metadados de recurso. 

### <a name="gateway-resource-metadata"></a>Metadados do recurso de gateway

Um gateway é declarado com os metadados seguintes:
* `apiVersion` -tem de ser definido para "2018-09-01-preview" (ou mais tarde, no futuro)
* `name` -um nome de cadeia de caracteres para este gateway
* `type` - "Microsoft.ServiceFabricMesh/gateways"
* `location` -deve ser definido para a localização da sua aplicação / rede; Normalmente, será uma referência para o parâmetro de localização na sua implementação
* `dependsOn` -a rede para o qual este gateway irá servir como um ponto de entrada para

Eis o aspeto que num modelo de implementação Azure Resource Manager (JSON): 

```json
{
  "apiVersion": "2018-09-01-preview",
  "name": "myGateway",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [
    "Microsoft.ServiceFabricMesh/networks/myNetwork"
  ],
  "properties": {
    [...]
  }
}
```

### <a name="gateway-properties"></a>Propriedades do gateway

A secção de propriedades é utilizada para definir as redes entre os quais o gateway está e as regras de encaminhamento de pedidos. 

#### <a name="source-and-destination-network"></a>Rede de origem e de destino 

Cada gateway requer um `sourceNetwork` e `destinationNetwork`. A rede de origem é definida como a rede a partir do qual a sua aplicação vai receber pedidos de entrada. Sua propriedade name deve sempre ser definida como "Aberto". A rede de destino é a rede que os pedidos de destino. O valor de nome para que isso deve ser definido para o nome do recurso de rede local da sua aplicação (deve incluir uma referência completa para o recurso). Veja abaixo para uma configuração de exemplo da aparência de uma implementação numa rede chamada "myNetwork".

```json 
"properties": {
  "description": "Service Fabric Mesh Gateway",
  "sourceNetwork": {
    "name": "Open"
  },
  "destinationNetwork": {
    "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'myNetwork')]"
  },
  [...]
}
```

#### <a name="rules"></a>Regras 

Um gateway pode ter o encaminhamento de várias regras que especificam o tráfego de entrada como serão processadas. Uma regra de roteamento define a relação entre a porta de escuta e o ponto de extremidade de destino para um determinado aplicativo. Para regras de encaminhamento de TCP, existe um mapeamento 1:1 entre o ponto de extremidade: porta. Para regras de encaminhamento de HTTP, pode definir regras de encaminhamento mais complexas, examine o caminho do pedido e, opcionalmente, cabeçalhos, decidir como o pedido será encaminhado. 

Regras de encaminhamento são especificadas por porta. Cada porta de entrada tem seu próprio conjunto de regras de dentro da seção de propriedades de sua configuração de gateway. 

#### <a name="tcp-routing-rules"></a>Regras de encaminhamento de TCP 

Uma regra de roteamento de TCP inclui as seguintes propriedades: 
* `name` -referência para a regra que pode ser qualquer cadeia de caracteres da sua preferência 
* `port` -porta a escutar para pedidos recebidos 
* `destination` -especificação de ponto final que inclui `applicationName`, `serviceName`, e `endpointName`, para onde os pedidos têm de ser encaminhado para

Aqui está uma regra de encaminhamento de TCP de exemplo:

```json
"properties": {
  [...]
  "tcp": [
    {
      "name": "web",
      "port": 80,
      "destination": {
        "applicationName": "myApp",
        "serviceName": "myService",
        "endpointName": "myListener"
      }
    }
  ]
}
```


#### <a name="http-routing-rules"></a>Regras de encaminhamento de HTTP 

Uma regra de encaminhamento HTTP inclui as seguintes propriedades: 
* `name` -referência para a regra que pode ser qualquer cadeia de caracteres da sua preferência 
* `port` -porta a escutar para pedidos recebidos 
* `hosts` -uma matriz de políticas que se aplicam a pedidos a chegar para vários "anfitriões" na porta especificada acima. Os anfitriões são o conjunto de aplicações e serviços que podem estar em execução na rede e podem servir pedidos recebidos, ou seja, uma aplicação web. Políticas de anfitrião são interpretadas em ordem, pelo que deve criar o seguinte em decrescente níveis de especificidade
    * `name` -o nome DNS do anfitrião para o qual são especificadas as seguintes regras de encaminhamento. Utilizar "*" aqui seria criar regras de encaminhamento para todos os anfitriões.
    * `routes` -uma matriz de políticas para este anfitrião específico
        * `match` -especificação da estrutura de pedido de entrada para esta regra aplicar, com base num `path`
            * `path` -contém um `value` (URI de entrada), `rewrite` (como pretende que o pedido para serem reencaminhados) e um `type` (atualmente apenas ser "Do prefixo")
            * `header` -é uma matriz opcional de valores de cabeçalhos para corresponder no cabeçalho da solicitação que, se o pedido corresponde a especificação de caminho (acima).
              * cada entrada contém `name` (nome do cabeçalho para corresponder à cadeia de caracteres), `value` (cadeia de valor do cabeçalho no pedido) e um `type` (atualmente apenas ser "Exact")
        * `destination` -Se a solicitação corresponder, será encaminhado para este destino, o que é especificado com um `applicationName`, `serviceName`, e `endpointName`

Aqui está uma regra de encaminhamento o exemplo HTTP que seria se aplicam a pedidos de chegar na porta 80, para todos os anfitriões servidos por aplicações nesta rede. Se o URL do pedido tem uma estrutura que corresponde à especificação do caminho, ou seja, `<IPAddress>:80/pickme/<requestContent>`, em seguida, será direcionado para o `myListener` ponto final.  

```json
"properties": {
  [...]
  "http": [
    {
      "name": "web",
      "port": 80,
      "hosts": [
        {
          "name": "*",
          "routes": [
            {
              "match": {
                "path": {
                  "value": "/pickme",
                  "rewrite": "/",
                  "type": "Prefix"
                }
              },
              "destination": {
                "applicationName": "meshApp",
                "serviceName": "myService",
                "endpointName": "myListener"
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="sample-config-for-a-gateway-resource"></a>Configuração de exemplo para um recurso de Gateway 

Eis o aspeto de uma configuração de recursos completa do Gateway (isto é adaptado do exemplo de entrada disponível no [repositório de exemplos de malha](https://github.com/Azure-Samples/service-fabric-mesh/blob/2018-09-01-preview/templates/ingress/meshingress.linux.json)):

```json
{
  "apiVersion": "2018-09-01-preview",
  "name": "ingressGatewayLinux",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [
    "Microsoft.ServiceFabricMesh/networks/meshNetworkLinux"
  ],
  "properties": {
    "description": "Service Fabric Mesh Gateway for Linux mesh samples.",
    "sourceNetwork": {
      "name": "Open"
    },
    "destinationNetwork": {
      "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'meshNetworkLinux')]"
    },
    "http": [
      {
        "name": "web",
        "port": 80,
        "hosts": [
          {
            "name": "*",
            "routes": [
              {
                "match": {
                  "path": {
                    "value": "/hello",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {
                  "applicationName": "meshAppLinux",
                  "serviceName": "helloWorldService",
                  "endpointName": "helloWorldListener"
                }
              },
              {
                "match": {
                  "path": {
                    "value": "/counter",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {
                  "applicationName": "meshAppLinux",
                  "serviceName": "counterService",
                  "endpointName": "counterServiceListener"
                }
              }
            ]
          }
        ]
      }
    ]
  }
}
```

Este gateway está configurado para uma aplicação do Linux, "meshAppLinux", que consiste em, pelo menos, dois serviços, "helloWorldService" e "counterService", que fica à escuta na porta 80. Consoante a estrutura do URL do pedido a receber, ele irá encaminhar o pedido para um destes serviços. 
* "<IPAddress>: 80/helloWorld/\<pedido\>" resultaria numa solicitação a ser direcionada para o "helloWorldListener" no helloWorldService. 
* "<IPAddress>: 80/contador/\<pedido\>" resultaria numa solicitação a ser direcionada para o "counterListener" no counterService. 

## <a name="next-steps"></a>Passos Seguintes
* Implementar o [exemplo de entrada](https://github.com/Azure-Samples/service-fabric-mesh/tree/2018-09-01-preview/templates/ingress) para ver gateways em ação
