---
title: Configurar um gateway para rotear solicitações
description: Saiba como configurar o gateway que manipula o tráfego de entrada para seus aplicativos em execução na malha de Service Fabric.
author: dkkapur
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: ec408403d4baa0f211c6bfe867a15c96513693cb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75461966"
---
# <a name="configure-a-gateway-resource-to-route-requests"></a>Configurar um recurso de gateway para rotear solicitações

Um recurso de gateway é usado para rotear o tráfego de entrada para a rede que hospeda seu aplicativo. Configure-o para especificar regras por meio das quais as solicitações são direcionadas para serviços ou pontos de extremidade específicos com base na estrutura da solicitação. Consulte [introdução à rede na malha Service Fabric](service-fabric-mesh-networks-and-gateways.md) para obter mais informações sobre redes e gateways na malha. 

Os recursos de gateway precisam ser declarados como parte do modelo de implantação (JSON ou YAML) e dependem de um recurso de rede. Este documento descreve as várias propriedades que podem ser definidas para o gateway e abrange uma configuração de gateway de exemplo.

## <a name="options-for-configuring-your-gateway-resource"></a>Opções para configurar o recurso de gateway

Como o recurso de gateway serve como uma ponte entre a rede do seu aplicativo e a rede da infraestrutura subjacente (a rede `open`). Você só precisa configurar um (na visualização de malha, há um limite de um gateway por aplicativo). A declaração do recurso consiste em duas partes principais: metadados de recurso e as propriedades. 

### <a name="gateway-resource-metadata"></a>Metadados de recurso de gateway

Um gateway é declarado com os seguintes metadados:
* `apiVersion`-precisa ser definido como "2018-09-01-Preview" (ou posterior, no futuro)
* `name`-um nome de cadeia de caracteres para este gateway
* `type`-"Microsoft. ServiceFabricMesh/gateways"
* `location`-deve ser definido como o local do seu aplicativo/rede; geralmente será uma referência ao parâmetro Location na sua implantação
* `dependsOn`-a rede para a qual esse gateway servirá como um ponto de entrada para

Veja como ele se parece em um modelo de implantação de Azure Resource Manager (JSON): 

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

A seção Propriedades é usada para definir as redes entre as quais o gateway está e as regras para roteamento de solicitações. 

#### <a name="source-and-destination-network"></a>Rede de origem e de destino 

Cada gateway requer um `sourceNetwork` e `destinationNetwork`. A rede de origem é definida como a rede da qual seu aplicativo receberá solicitações de entrada. Sua propriedade Name sempre deve ser definida como "Open". A rede de destino é a rede para a qual as solicitações estão sendo direcionadas. O valor de nome para isso deve ser definido como o nome do recurso da rede local do seu aplicativo (deve incluir referência completa ao recurso). Veja abaixo uma configuração de exemplo de como isso se parece com uma implantação em uma rede chamada "mynetwork".

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

Um gateway pode ter várias regras de roteamento que especificam como o tráfego de entrada será tratado. Uma regra de roteamento define a relação entre a porta de escuta e o ponto de extremidade de destino para um determinado aplicativo. Para as regras de roteamento TCP, há um mapeamento 1:1 entre porta: ponto de extremidade. Para as regras de roteamento HTTP, você pode definir regras de roteamento mais complexas que examinam o caminho da solicitação e, opcionalmente, os cabeçalhos para decidir como a solicitação será roteada. 

As regras de roteamento são especificadas por porta. Cada porta de entrada tem sua própria matriz de regras na seção Propriedades da configuração do seu gateway. 

#### <a name="tcp-routing-rules"></a>Regras de roteamento TCP 

Uma regra de roteamento TCP consiste nas seguintes propriedades: 
* `name`-referência à regra que pode ser qualquer cadeia de caracteres de sua escolha 
* `port`-porta a ser escutada para solicitações de entrada 
* especificação de ponto de extremidade `destination` que inclui `applicationName`, `serviceName`e `endpointName`, para onde as solicitações precisam ser roteadas

Aqui está um exemplo de regra de roteamento de TCP:

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


#### <a name="http-routing-rules"></a>Regras de roteamento HTTP 

Uma regra de roteamento HTTP consiste nas seguintes propriedades: 
* `name`-referência à regra que pode ser qualquer cadeia de caracteres de sua escolha 
* `port`-porta a ser escutada para solicitações de entrada 
* `hosts`-uma matriz de políticas que se aplica a solicitações recebidas para os vários "hosts" na porta especificada acima. Os hosts são o conjunto de aplicativos e serviços que podem estar em execução na rede e podem atender a solicitações de entrada, ou seja, um aplicativo Web. As políticas de host são interpretadas em ordem, portanto, você deve criar o seguinte em níveis decrescentes de especificidade
    * `name`-o nome DNS do host para o qual as regras de roteamento a seguir são especificadas. Usar "*" aqui criaria regras de roteamento para todos os hosts.
    * `routes`-uma matriz de políticas para este host específico
        * `match`-especificação da estrutura de solicitação de entrada para esta regra a ser aplicada, com base em um `path`
            * `path`-contém um `value` (URI de entrada), `rewrite` (como você deseja que a solicitação seja encaminhada) e um `type` (no momento, só pode ser "prefix")
            * `header`-é uma matriz opcional de valores de cabeçalhos para corresponder ao cabeçalho da solicitação que se a solicitação corresponder à especificação do caminho (acima).
              * cada entrada contém `name` (nome da cadeia de caracteres do cabeçalho para corresponder), `value` (valor da cadeia de caracteres do cabeçalho na solicitação) e um `type` (no momento, só pode ser "exato")
        * `destination`-se a solicitação corresponder, ela será roteada para esse destino, que é especificado usando um `applicationName`, `serviceName`e `endpointName`

Aqui está um exemplo de regra de roteamento HTTP que se aplica a solicitações recebidas na porta 80, para todos os hosts servidos por aplicativos nesta rede. Se a URL da solicitação tiver uma estrutura que corresponda à especificação de caminho, ou seja, `<IPAddress>:80/pickme/<requestContent>`, ela será direcionada para o ponto de extremidade de `myListener`.  

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

## <a name="sample-config-for-a-gateway-resource"></a>Configuração de exemplo para um recurso de gateway 

Aqui está a aparência de uma configuração de recurso de gateway completa (isso é adaptado do exemplo de entrada disponível no [repositório de amostras de malha](https://github.com/Azure-Samples/service-fabric-mesh/blob/2018-09-01-preview/templates/ingress/meshingress.linux.json)):

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

Esse gateway está configurado para um aplicativo Linux, "meshAppLinux", que consiste em pelo menos dois serviços, "helloWorldService" e "MyService", que escuta na porta 80. Dependendo da estrutura de URL da solicitação de entrada, ela roteará a solicitação para um desses serviços. 
* "\<IPAddress >: 80/helloWorld/\<solicitação\>" resultaria em uma solicitação sendo direcionada para o "helloWorldListener" no helloWorldService. 
* "\<IPAddress >: 80/contador/\<solicitação\>" resultaria em uma solicitação sendo direcionada para o "ouvinte" no mesmo serviço. 

## <a name="next-steps"></a>Passos seguintes
* Implantar o [exemplo de entrada](https://github.com/Azure-Samples/service-fabric-mesh/tree/2018-09-01-preview/templates/ingress) para ver os gateways em ação
