---
title: Descrição geral do encaminhamento de conteúdos baseado em URL do Gateway de Aplicação do Azure
description: Este artigo fornece uma visão geral do roteamento de conteúdo baseado em URL Aplicativo Azure gateway, configuração de UrlPathMap e regra PathBasedRouting.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 09/10/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 2234ae4ce8257559f78d6aa50ecae59ae742ba33
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910013"
---
# <a name="url-path-based-routing-overview"></a>Descrição geral do Encaminhamento Baseado no Caminho do URL

O Encaminhamento Baseado no Caminho do URL permite-lhe encaminhar o tráfego para agrupamentos de servidores de back-end com base nos Caminhos de URL. 

Um dos cenários consiste em encaminhar pedidos de diferentes tipos de conteúdo para diversos agrupamentos de servidores de back-end.

No exemplo a seguir, o gateway de aplicativo está servindo o tráfego para contoso.com de três pools de servidores back-end, por exemplo: VideoServerPool, ImageServerPool e DefaultServerPool.

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

As solicitações de\:http//contoso.com/Video/* são roteadas para VideoServerPool e\:http//contoso.com/images/* são roteadas para ImageServerPool. É selecionado o DefaultServerPool se nenhum dos padrões de caminho corresponder.

> [!IMPORTANT]
> As regras são processadas pela ordem em que são apresentadas no portal. Antes de configurar um serviço de escuta básico, recomenda-se vivamente que configure serviços de escuta de múltiplos sites.  Desta forma, assegura que o tráfego é encaminhado para o back-end certo. Se for apresentado primeiro um serviço de escuta básico e este corresponde a um pedido de entrada, o pedido é processado por esse serviço de escuta.

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

### <a name="pathpattern"></a>PathPattern

PathPattern é uma lista de padrões de caminho para correspondência. Cada um deles tem de começar com / e o único local onde "*" é permitido é no fim depois de "/". A cadeia de caracteres alimentada para o correspondente de caminho não inclui nenhum texto após o primeiro? ou #, e esses caracteres não são permitidos aqui. Caso contrário, todos os caracteres permitidos em uma URL são permitidos em PathPattern.

Os padrões com suporte dependem de você implantar o Application Gateway v1 ou v2:

#### <a name="v1"></a>v1

As regras de caminho não diferenciam maiúsculas de minúsculas.

|padrão de caminho v1  |Tem suporte?  |
|---------|---------|
|`/images/*`     |sim|
|`/images*`     |não|
|`/images/*.jpg`     |não|
|`/*.jpg`     |não|
|`/Repos/*/Comments/*`     |não|
|`/CurrentUser/Comments/*`     |sim|

#### <a name="v2"></a>v2

As regras de caminho não diferenciam maiúsculas de minúsculas.

|padrão de caminho v2  |Tem suporte?  |
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

## <a name="next-steps"></a>Passos Seguintes

Depois de saber mais sobre o encaminhamento de conteúdo baseado em URL, aceda a [Criar um gateway de aplicação com encaminhamento baseado em URL](create-url-route-portal.md) para criar um gateway de aplicação com regras de encaminhamento do URL.
