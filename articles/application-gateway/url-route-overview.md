---
title: Descrição geral do encaminhamento de conteúdos baseado em URL do Gateway de Aplicação do Azure
description: Este artigo fornece uma visão geral do encaminhamento de conteúdo baseado em URL baseado em Url da Aplicação Azure, configuração UrlPathMap e regra pathBasedRouting.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 09/10/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: e20acb131b1a091fef858dab34705f4a8d3b4c4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77251843"
---
# <a name="url-path-based-routing-overview"></a>Descrição geral do Encaminhamento Baseado no Caminho do URL

O Encaminhamento Baseado no Caminho do URL permite-lhe encaminhar o tráfego para agrupamentos de servidores de back-end com base nos Caminhos de URL. 

Um dos cenários consiste em encaminhar pedidos de diferentes tipos de conteúdo para diversos agrupamentos de servidores de back-end.

No exemplo seguinte, o Gateway de Aplicação está a enviar tráfego para contoso.com a partir de três agrupamentos de servidores de back-end, como, por exemplo: VideoServerPool, ImageServerPool e DefaultServerPool.

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

Os pedidos\:para http //contoso.com/video/* são encaminhados\:para VideoServerPool, e http/contoso.com/images/* são encaminhados para ImageServerPool. É selecionado o DefaultServerPool se nenhum dos padrões de caminho corresponder.

> [!IMPORTANT]
> Para o V1 SKU, as regras são processadas na ordem em que estão listadas no portal. Se for apresentado primeiro um serviço de escuta básico e este corresponde a um pedido de entrada, o pedido é processado por esse serviço de escuta. Para o V2 SKU, os fósforos exatos têm maior precedência. No entanto, é altamente recomendado configurar os ouvintes multi-sites primeiro antes de configurar um ouvinte básico. Desta forma, assegura que o tráfego é encaminhado para o back-end certo.

## <a name="urlpathmap-configuration-element"></a>Elemento de configuração UrlPathMap

O elemento urlPathMap é utilizado para especificar padrões de Caminho para mapeamentos de agrupamentos de servidores de back-end. O seguinte exemplo de código é o fragmento do elemento UrlPathMap do ficheiro de modelo.

```json
"urlPathMaps": [{
    "name": "{urlpathMapName}",
    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/urlPathMaps/{urlpathMapName}",
    "properties": {
        "defaultBackendAddressPool": {
            "id": "/subscriptions/    {subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName1}"
        },
        "defaultBackendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpSettingsList/{settingname1}"
        },
        "pathRules": [{
            "name": "{pathRuleName}",
            "properties": {
                "paths": [
                    "{pathPattern}"
                ],
                "backendAddressPool": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName2}"
                },
                "backendHttpsettings": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpsettingsList/{settingName2}"
                }
            }
        }]
    }
}]
```

### <a name="pathpattern"></a>Padrão de caminho

PathPattern é uma lista de padrões de caminho para combinar. Cada um deles tem de começar com / e o único local onde "*" é permitido é no fim depois de "/". A corda alimentada ao matcher do caminho não inclui qualquer texto após o primeiro? ou #, e esses chars não são permitidos aqui. Caso contrário, quaisquer caracteres permitidos num URL são permitidos no PathPattern.

Os padrões suportados dependem se implementa o Application Gateway v1 ou v2:

#### <a name="v1"></a>v1

As regras do caminho são insensíveis.

|padrão de caminho v1  |É apoiado?  |
|---------|---------|
|`/images/*`     |sim|
|`/images*`     |não|
|`/images/*.jpg`     |não|
|`/*.jpg`     |não|
|`/Repos/*/Comments/*`     |não|
|`/CurrentUser/Comments/*`     |sim|

#### <a name="v2"></a>v2

As regras do caminho são insensíveis.

|padrão de caminho v2  |É apoiado?  |
|---------|---------|
|`/images/*`     |sim|
|`/images*`     |sim|
|`/images/*.jpg`     |não|
|`/*.jpg`     |não|
|`/Repos/*/Comments/*`     |não|
|`/CurrentUser/Comments/*`     |sim|

Pode dar saída a um [modelo do Resource Manager através do encaminhamento baseado em URL](https://azure.microsoft.com/documentation/templates/201-application-gateway-url-path-based-routing) para obter mais informações.

## <a name="pathbasedrouting-rule"></a>Regra PathBasedRouting

A RequestRoutingRule do tipo PathBasedRouting é utilizada para vincular um serviço de escuta a um UrlPathMap. Todos os pedidos que são recebidos para este serviço de escuta são encaminhados com base na política especificada no urlPathMap.
Fragmento da regra PathBasedRouting:

```json
"requestRoutingRules": [
    {

"name": "{ruleName}",
"id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/requestRoutingRules/{ruleName}",
"properties": {
    "ruleType": "PathBasedRouting",
    "httpListener": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/httpListeners/<listenerName>"
    },
    "urlPathMap": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/ urlPathMaps/{urlpathMapName}"
    },

}
    }
]
```

## <a name="next-steps"></a>Passos seguintes

Depois de saber mais sobre o encaminhamento de conteúdo baseado em URL, aceda a [Criar um gateway de aplicação com encaminhamento baseado em URL](create-url-route-portal.md) para criar um gateway de aplicação com regras de encaminhamento do URL.
