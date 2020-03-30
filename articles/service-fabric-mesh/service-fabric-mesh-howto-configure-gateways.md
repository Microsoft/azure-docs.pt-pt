---
title: Configure um Gateway para solicitar a rota
description: Aprenda a configurar o portal que trata o tráfego de entrada para a sua(s) aplicação(s) em malha de tecido de serviço.
author: dkkapur
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: ec408403d4baa0f211c6bfe867a15c96513693cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75461966"
---
# <a name="configure-a-gateway-resource-to-route-requests"></a>Configure um recurso Gateway para pedidos de rotas

Um recurso Gateway é usado para direcionar o tráfego de entrada para a rede que abriga a sua aplicação. Configure-o para especificar regras através das quais os pedidos são direcionados para serviços ou pontos finais específicos com base na estrutura do pedido. Consulte [introdução à rede em Malha](service-fabric-mesh-networks-and-gateways.md) de Tecido de Serviço para obter mais informações sobre redes e gateways em Malha. 

Os recursos gateway devem ser declarados como parte do seu modelo de implantação (JSON ou yaml), e dependem de um recurso de rede. Este documento descreve as várias propriedades que podem ser definidas para o seu gateway e cobre uma amostra de gateway config.

## <a name="options-for-configuring-your-gateway-resource"></a>Opções para configurar o seu recurso Gateway

Uma vez que o recurso Gateway serve de ponte entre a rede `open` da sua aplicação e a rede de infraestruturas subjacentes (a rede). Só deve configurar um (na pré-visualização da Malha, existe um limite de um gateway por app). A declaração para o recurso é constituída por duas partes principais: metadados de recursos e propriedades. 

### <a name="gateway-resource-metadata"></a>Metadados de recursos gateway

É declarada uma porta de entrada com os seguintes metadados:
* `apiVersion`- tem de ser definido para "2018-09-01-preview" (ou mais tarde, no futuro)
* `name`- um nome de corda para este portal
* `type`- "Microsoft.ServiceFabricMesh/gateways"
* `location`- deve ser definido para a localização da sua app/rede; normalmente será uma referência ao parâmetro de localização na sua implantação
* `dependsOn`- a rede para a qual esta porta de entrada servirá de ponto de entrada para

Eis o que parece num modelo de implementação do Azure Resource Manager (JSON): 

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

A secção de propriedades é usada para definir as redes entre as quais se situa o gateway, e as regras para pedidos de encaminhamento. 

#### <a name="source-and-destination-network"></a>Rede de origem e destino 

Cada porta de `sourceNetwork` `destinationNetwork`entrada requer a e . A rede de origem é definida como a rede a partir da qual a sua aplicação receberá pedidos de entrada. A sua propriedade de nome deve ser sempre definida para "Open". A rede de destino é a rede que os pedidos estão a ser alvo. O valor do nome para este deve ser definido para o nome de recurso da rede local da sua aplicação (deve incluir referência completa ao recurso). Veja abaixo uma amostra do que isto parece para uma implantação numa rede chamada "myNetwork".

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

Um gateway pode ter várias regras de encaminhamento que especificam como o tráfego de entrada será tratado. Uma regra de encaminhamento define a relação entre o porto de escuta e o ponto final do destino para uma determinada aplicação. Para as regras de encaminhamento de TCP, há um mapeamento 1:1 entre Port:Endpoint. Para as regras de encaminhamento http, você pode definir regras de encaminhamento mais complexas que examinam o caminho do pedido, e opcionalmente cabeçalhos, para decidir como o pedido será encaminhado. 

As regras de encaminhamento são especificadas por porta. Cada porta de entrada tem o seu próprio conjunto de regras dentro da secção de propriedades do seu gateway config. 

#### <a name="tcp-routing-rules"></a>Regras de encaminhamento de TCP 

Uma regra de encaminhamento de TCP consiste nas seguintes propriedades: 
* `name`- referência à regra que pode ser qualquer cadeia da sua escolha 
* `port`- porto para ouvir os pedidos de entrada 
* `destination`- especificação de `applicationName` `serviceName`ponto `endpointName`final que inclua, e, para onde os pedidos devem ser encaminhados para

Aqui está uma regra de encaminhamento de TCP exemplo:

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


#### <a name="http-routing-rules"></a>Regras de encaminhamento http 

Uma regra de encaminhamento HTTP consiste nas seguintes propriedades: 
* `name`- referência à regra que pode ser qualquer cadeia da sua escolha 
* `port`- porto para ouvir os pedidos de entrada 
* `hosts`- um conjunto de políticas aplicáveis aos pedidos que se aplicam aos vários "anfitriões" da porta acima especificada. Os anfitriões são o conjunto de aplicações e serviços que podem estar a funcionar na rede e podem servir pedidos de entrada, ou seja, uma aplicação web. As políticas de acolhimento são interpretadas em ordem, por isso deve criar o seguinte em níveis descendentes de especificidade
    * `name`- o nome DNS do hospedeiro para o qual são especificadas as seguintes regras de encaminhamento. Usar "*" aqui criaria regras de encaminhamento para todos os anfitriões.
    * `routes`- uma série de políticas para este anfitrião específico
        * `match`- especificação da estrutura de pedido de entrada para que esta regra se aplique, com base num`path`
            * `path`- contém `value` um (URI `rewrite` de entrada), (como pretende que `type` o pedido seja reencaminhado), e um (atualmente só pode ser "Prefixo")
            * `header`- é um conjunto opcional de valores de cabeçalhos para corresponder no cabeçalho do pedido que, se o pedido corresponder à especificação do caminho (acima).
              * cada entrada `name` contém (nome de corda `value` do cabeçalho a combinar), (valor de cadeia do cabeçalho no pedido) e um `type` (atualmente só pode ser "Exato")
        * `destination`- se o pedido corresponder, será encaminhado para este destino, `applicationName` `serviceName`que é especificado com um , e`endpointName`

Aqui está uma regra de encaminhamento HTTP que se aplicaria aos pedidos que chegam no porto 80, a todos os anfitriões servidos por apps nesta rede. Se o URL de pedido tiver uma estrutura que `<IPAddress>:80/pickme/<requestContent>`corresponda à especificação do `myListener` caminho, ou seja, então será direcionado para o ponto final.  

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

## <a name="sample-config-for-a-gateway-resource"></a>Figo de amostra para um recurso Gateway 

Aqui está o que um config de recurso Gateway completo parece (isto é adaptado a partir da amostra de entrada disponível no repo de [amostras](https://github.com/Azure-Samples/service-fabric-mesh/blob/2018-09-01-preview/templates/ingress/meshingress.linux.json)de malha ):

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

Este portal está configurado para uma aplicação Linux, "meshAppLinux", que consiste em pelo menos dois serviços, "helloWorldService" e "counterService", que escuta na porta 80. Dependendo da estrutura url do pedido de entrada, irá encaminhar o pedido para um destes serviços. 
* "\<IPAddress>:80/helloWorld/\<request\>" resultaria num pedido dirigido ao "helloWorldListener" no helloWorldService. 
* "\<IPAddress>:80/counter/request\<\>" resultaria num pedido dirigido ao "contralistener" no contraserviço. 

## <a name="next-steps"></a>Passos seguintes
* Implementar a [amostra Ingress](https://github.com/Azure-Samples/service-fabric-mesh/tree/2018-09-01-preview/templates/ingress) para ver gateways em ação
