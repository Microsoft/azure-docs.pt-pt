---
title: Utilize o endereço IP privado para encaminhamento interno para um ponto final ingresss
description: Este artigo fornece informações sobre como usar iPs privados para encaminhamento interno e, assim, expondo o ponto final ingress dentro de um cluster para o resto do VNet.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 8be37ed1da0da4da3db43ef4c1cd01ed962f24ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397313"
---
# <a name="use-private-ip-for-internal-routing-for-an-ingress-endpoint"></a>Use IP privado para encaminhamento interno para um ponto final ingress 

Esta funcionalidade permite expor o ponto final de entrada dentro do `Virtual Network` uso de um IP privado.

## <a name="pre-requisites"></a>Pré-requisitos  
Gateway de aplicação com [configuração IP privada](./configure-application-gateway-with-private-frontend-ip.md)

Existem duas formas de configurar o controlador para usar o IP privado para a entrada,

## <a name="assign-to-a-particular-ingress"></a>Atribuir a uma entrada particular
Para expor uma entrada particular sobre o IP privado, use anotação [`appgw.ingress.kubernetes.io/use-private-ip`](./ingress-controller-annotations.md#use-private-ip) em Ingress.

### <a name="usage"></a>Utilização
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

Para gateways de aplicação sem um IP privado, Ingresses anotado com `appgw.ingress.kubernetes.io/use-private-ip: "true"` será ignorado. Isto será indicado no evento de entrada e no registo de pods AGIC.

* Erro como indicado no Evento de Entrada

    ```bash
    Events:
    Type     Reason       Age               From                                                                     Message
    ----     ------       ----              ----                                                                     -------
    Warning  NoPrivateIP  2m (x17 over 2m)  azure/application-gateway, prod-ingress-azure-5c9b6fcd4-bctcb  Ingress default/hello-world-ingress requires Application Gateway 
    applicationgateway3026 has a private IP address
    ```

* Erro tal como indicado nos registos AGIC

    ```bash
    E0730 18:57:37.914749       1 prune.go:65] Ingress default/hello-world-ingress requires Application Gateway applicationgateway3026 has a private IP address
    ```


## <a name="assign-globally"></a>Atribuir Globalmente
No caso de restringir todas as Ingresses a serem expostas durante o IP privado, utilização `appgw.usePrivateIP: true` em `helm` config.

### <a name="usage"></a>Utilização
```yaml
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>
    usePrivateIP: true
```

Isto fará com que o controlador de entrada filtra as configurações do endereço IP para um IP privado ao configurar os ouvintes frontend no Gateway de aplicação.
A AGIC entrará em pânico e colidirá se `usePrivateIP: true` e nenhum IP privado for atribuído.

> [!NOTE]
> Application Gateway v2 SKU requer um IP público. Se necessitar que o Gateway de Aplicação seja privado, anexe [`Network Security Group`](../virtual-network/network-security-groups-overview.md) a a sub-rede do Gateway de aplicação para restringir o tráfego.