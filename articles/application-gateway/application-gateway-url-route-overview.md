---
title: Descrição geral do encaminhamento de conteúdos baseado em URL do Gateway de Aplicação do Azure
description: Esta página fornece uma visão geral do encaminhamento conteúdo baseado em URL de Gateway de aplicação do Azure, configuração UrlPathMap e da regra PathBasedRouting.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 1/8/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 2f0bd0a20b866d342802014f1034a7ed9d939d53
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792247"
---
# <a name="url-path-based-routing-overview"></a>Descrição geral do Encaminhamento Baseado no Caminho do URL

O Encaminhamento Baseado no Caminho do URL permite-lhe encaminhar o tráfego para agrupamentos de servidores de back-end com base nos Caminhos de URL. 

Um dos cenários consiste em encaminhar pedidos de diferentes tipos de conteúdo para diversos agrupamentos de servidores de back-end.

No exemplo a seguir, o Gateway de aplicação está a enviar tráfego para contoso.com a partir de três agrupamentos de servidor back-end por exemplo: VideoServerPool, ImageServerPool e DefaultServerPool.

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

Pedidos de http\://contoso.com/video/* são encaminhados para VideoServerPool e http\://contoso.com/images/* são encaminhados para ImageServerPool. É selecionado o DefaultServerPool se nenhum dos padrões de caminho corresponder.

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

PathPattern é uma lista de padrões de caminho corresponder. Cada um deles tem de começar com / e o único local onde "*" é permitido é no fim depois de "/". A cadeia introduzida na ferramenta de correspondência do caminho não inclui qualquer texto depois do primeiro? ou, # sendo que esses carateres não são permitidas aqui. Caso contrário, são permitidas quaisquer carateres permitidos num URL PathPattern.

Os padrões suportados dependem se implementar o Gateway de aplicação v1 ou v2:

#### <a name="v1"></a>v1

As regras de caminho diferenciam maiúsculas de minúsculas.

|padrão do caminho v1  |É suportada?  |
|---------|---------|
|`/images/*`     |sim|
|`/images*`     |não|
|`/images/*.jpg`     |não|
|`/*.jpg`     |não|
|`/Repos/*/Comments/*`     |não|
|`/CurrentUser/Comments/*`     |sim|

#### <a name="v2"></a>v2

As regras de caminho são maiúsculas de minúsculas.

|padrão do caminho v2  |É suportada?  |
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

Depois de saber mais sobre o encaminhamento de conteúdo baseado em URL, aceda a [Criar um gateway de aplicação com encaminhamento baseado em URL](application-gateway-create-url-route-portal.md) para criar um gateway de aplicação com regras de encaminhamento do URL.
