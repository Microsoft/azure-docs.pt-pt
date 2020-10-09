---
title: Configurar um Gateway para pedidos de rota
description: Saiba como configurar o gateway que trata do tráfego de entrada para as suas aplicações em execução na Rede de Tecido de Serviço.
author: georgewallace
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: aa3ac9d8835cd17387346bb29b3e7c30f286cd1f
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91839729"
---
# <a name="configure-a-gateway-resource-to-route-requests"></a>Configure um recurso Gateway para encaminhar pedidos

Um recurso Gateway é usado para encaminhar o tráfego de entrada para a rede que abriga a sua aplicação. Configure-o para especificar as regras através das quais os pedidos são direcionados para serviços específicos ou pontos finais com base na estrutura do pedido. Consulte [Introdução à rede na malha de tecido de serviço](service-fabric-mesh-networks-and-gateways.md) para obter mais informações sobre redes e gateways na Malha. 

Os recursos gateway precisam de ser declarados como parte do seu modelo de implantação (JSON ou yaml), e dependem de um recurso de Rede. Este documento descreve as várias propriedades que podem ser definidas para o seu gateway e cobre uma amostra de gateway config.

## <a name="options-for-configuring-your-gateway-resource"></a>Opções para configurar o seu recurso Gateway

Uma vez que o recurso Gateway serve de ponte entre a rede da sua aplicação e a rede da infraestrutura subjacente (a `open` rede). Só deve configurar um (na pré-visualização da Malha, existe um limite de um gateway por app). A declaração para o recurso é constituída por duas partes principais: metadados de recurso e propriedades. 

### <a name="gateway-resource-metadata"></a>Metadados de recursos gateway

Um portal é declarado com os seguintes metadados:
* `apiVersion` - tem de ser definido para "2018-09-01-pré-visualização" (ou mais tarde, no futuro)
* `name` - um nome de corda para este portal
* `type` - "Microsoft.ServiceFabricMesh/gateways"
* `location` - deve ser configurado para a localização da sua app/rede; geralmente será uma referência ao parâmetro de localização na sua implantação
* `dependsOn` - a rede para a qual esta porta de entrada servirá de ponto de entrada para

Aqui está o que parece num modelo de implementação do Azure Resource Manager (JSON): 

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

### <a name="gateway-properties"></a>Propriedades gateway

A secção de propriedades é usada para definir as redes entre as quais se encontra o gateway, e as regras para pedidos de encaminhamento. 

#### <a name="source-and-destination-network"></a>Rede de origem e destino 

Cada porta de entrada requer um `sourceNetwork` e `destinationNetwork` . A rede de origem é definida como a rede a partir da qual a sua aplicação receberá pedidos de entrada. A propriedade do seu nome deve ser sempre definida como "Open". A rede de destino é a rede que os pedidos estão a ser alvo. O valor do nome para este deve ser definido para o nome de recurso da rede local da sua aplicação (deve incluir referência completa ao recurso). Veja abaixo uma amostra config do que isto parece para uma implantação numa rede chamada "myNetwork".

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

Um gateway pode ter várias regras de encaminhamento que especificam como o tráfego de entrada será tratado. Uma regra de encaminhamento define a relação entre a porta de audição e o ponto final de destino para uma determinada aplicação. Para as regras de encaminhamento TCP, existe um mapeamento 1:1 entre Port:Endpoint. Para as regras de encaminhamento HTTP, pode definir regras de encaminhamento mais complexas que analisem o caminho do pedido, e cabeçalhos opcionalmente, para decidir como o pedido será encaminhado. 

As regras de encaminhamento são especificadas por porta. Cada porta de entrada tem o seu próprio conjunto de regras dentro da secção de propriedades do seu gateway config. 

#### <a name="tcp-routing-rules"></a>Regras de encaminhamento TCP 

Uma regra de encaminhamento TCP consiste nas seguintes propriedades: 
* `name` - referência à regra que pode ser qualquer cadeia à sua escolha 
* `port` - porta para ouvir os pedidos de entrada 
* `destination` - especificação de ponto final que `applicationName` `serviceName` inclua, `endpointName` e, para onde os pedidos devem ser encaminhados para

Aqui está uma regra de encaminhamento TCP exemplo:

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


#### <a name="http-routing-rules"></a>Regras de encaminhamento HTTP 

Uma regra de encaminhamento HTTP consiste nas seguintes propriedades: 
* `name` - referência à regra que pode ser qualquer cadeia à sua escolha 
* `port` - porta para ouvir os pedidos de entrada 
* `hosts` - um conjunto de políticas aplicáveis aos pedidos que chegam aos vários "anfitriões" do porto acima referido. Os anfitriões são o conjunto de aplicações e serviços que podem estar a ser geridos na rede e que podem servir pedidos de entrada, ou seja, uma aplicação web. As políticas de anfitrião são interpretadas por ordem, por isso deve criar os seguintes níveis de especificidade descendentes
    * `name` - o nome DNS do anfitrião para o qual são especificadas as seguintes regras de encaminhamento. Usar "*" aqui criaria regras de encaminhamento para todos os anfitriões.
    * `routes` - uma série de políticas para este hospedeiro específico
        * `match` - especificação da estrutura de pedido de entrada para que esta regra se aplique, com base numa `path`
            * `path` - contém um `value` (URI de entrada), `rewrite` (como pretende que o pedido seja reencaminhado), e um `type` (atualmente só pode ser "Prefix")
            * `header` - é um conjunto opcional de valores de cabeçalhos que correspondem no cabeçalho do pedido que, se o pedido corresponder à especificação do caminho (acima).
              * cada entrada contém `name` (nome de cadeia do cabeçalho a combinar), `value` (valor de cadeia do cabeçalho no pedido) e um `type` (atualmente só pode ser "Exact")
        * `destination` - se o pedido corresponder, será encaminhado para este destino, que é especificado através de `applicationName` um `serviceName` , e `endpointName`

Aqui está um exemplo de regra de encaminhamento HTTP que se aplicaria a pedidos que chegam ao porto 80, a todos os anfitriões servidos por apps nesta rede. Se o URL de pedido tiver uma estrutura que corresponda à especificação do caminho, `<IPAddress>:80/pickme/<requestContent>` ou seja, então será direcionado para o `myListener` ponto final.  

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

## <a name="sample-config-for-a-gateway-resource"></a>Config de amostra para um recurso Gateway 

Aqui está o que parece um recurso gateway completo config (isto é adaptado a partir da amostra de entrada disponível na [rede de amostras de malha repo):](https://github.com/Azure-Samples/service-fabric-mesh/blob/2018-09-01-preview/templates/ingress/meshingress.linux.json)

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

Esta porta de entrada está configurada para uma aplicação Linux, "meshAppLinux", que consiste em pelo menos dois serviços, "helloWorldService" e "counterService", que escuta no porto 80. Dependendo da estrutura URL do pedido de entrada, irá encaminhar o pedido para um destes serviços. 
* " \<IPAddress> :80/helloWorld/ \<request\> " resultaria num pedido dirigido ao "helloWorldListener" no helloWorldService. 
* \<IPAddress>":80/contador/ \<request\> " resultaria num pedido dirigido ao "counterListener" no balcão. 

## <a name="next-steps"></a>Passos seguintes
* Implementar a [amostra de Ingress](https://github.com/Azure-Samples/service-fabric-mesh/tree/2018-09-01-preview/templates/ingress) para ver gateways em ação
