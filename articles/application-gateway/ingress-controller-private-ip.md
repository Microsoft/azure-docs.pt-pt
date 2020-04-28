---
title: Utilize o endereço IP privado para encaminhamento interno para um ponto final de entrada
description: Este artigo fornece informações sobre como usar iPs privados para encaminhamento interno e, assim, expondo o ponto final de Ingress dentro de um cluster para o resto da VNet.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 570f28ce559ff1c1180ffaacb781b9120b1890a2
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "73795495"
---
# <a name="use-private-ip-for-internal-routing-for-an-ingress-endpoint"></a>Utilize IP privado para encaminhamento interno para um ponto final de Ingress 

Esta funcionalidade permite expor o ponto `Virtual Network` final de entrada dentro do IP privado.

## <a name="pre-requisites"></a>Pré-requisitos  
Gateway de aplicação com [configuração IP privada](https://docs.microsoft.com/azure/application-gateway/configure-application-gateway-with-private-frontend-ip)

Existem duas formas de configurar o controlador para usar IP privado para ingresso,

## <a name="assign-to-a-particular-ingress"></a>Atribuir a uma entrada particular
Para expor uma entrada particular sobre ip [`appgw.ingress.kubernetes.io/use-private-ip`](./ingress-controller-annotations.md#use-private-ip) privado, use anotação em Ingress.

### <a name="usage"></a>Utilização
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

Para Gateways de Aplicação sem UM IP `appgw.ingress.kubernetes.io/use-private-ip: "true"` privado, ingressos anotados com serão ignorados. Isto será indicado no evento de ingresso e no registo de cápsulas AGIC.

* Erro indicado no Evento Ingress

    ```bash
    Events:
    Type     Reason       Age               From                                                                     Message
    ----     ------       ----              ----                                                                     -------
    Warning  NoPrivateIP  2m (x17 over 2m)  azure/application-gateway, prod-ingress-azure-5c9b6fcd4-bctcb  Ingress default/hello-world-ingress requires Application Gateway 
    applicationgateway3026 has a private IP address
    ```

* Erro indicado nos Registos AGIC

    ```bash
    E0730 18:57:37.914749       1 prune.go:65] Ingress default/hello-world-ingress requires Application Gateway applicationgateway3026 has a private IP address
    ```


## <a name="assign-globally"></a>Atribuição Global
No caso de, a obrigação é restringir todas `appgw.usePrivateIP: true` as `helm` Ingresses a serem expostas durante o IP privado, uso em config.

### <a name="usage"></a>Utilização
```yaml
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>
    usePrivateIP: true
```

Isto fará com que o controlador de entrada filtrar as configurações do endereço IP para um IP privado ao configurar os ouvintes frontend no Gateway da Aplicação.
A AGIC entrará `usePrivateIP: true` em pânico e entrará em pânico se não for atribuído nenhum IP privado.

> [!NOTE]
> Application Gateway v2 SKU requer um IP público. Caso exija que o Gateway de [`Network Security Group`](https://docs.microsoft.com/azure/virtual-network/security-overview) Aplicação seja privado, fixe um à subnet do Gateway de Aplicação para restringir o tráfego.
