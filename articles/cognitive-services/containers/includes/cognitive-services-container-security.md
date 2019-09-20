---
title: Segurança do contentor
titleSuffix: Azure Cognitive Services
description: Saiba como proteger seu contêiner
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: dapine
ms.openlocfilehash: 0e9fa9146292bf7dabbbf06d3bb436aa6cd2e6e2
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71124096"
---
## <a name="azure-cognitive-services-container-security"></a>Segurança do contêiner de serviços cognitivas do Azure

A segurança deve ser um foco principal sempre que você estiver desenvolvendo aplicativos. A importância da segurança é uma métrica para o sucesso. Quando você está arquitetando uma solução de software que inclui contêineres de serviços cognitivas, é vital entender as limitações e os recursos disponíveis para você. Para obter mais informações, consulte [segurança do Azure][az-security].

> [!IMPORTANT]
> Por padrão, não há *nenhuma segurança* na API do contêiner de serviços cognitivas. O motivo disso é que, na maioria das vezes, o contêiner será executado como parte de um pod que é protegido do exterior por uma ponte de rede. No entanto, é possível habilitar a autenticação que funciona de forma idêntica à autenticação usada ao acessar [Serviços cognitivas baseados em nuvem][request-authentication].

O diagrama a seguir ilustra a abordagem padrão e **insegura** :

![Segurança do contentor](../media/container-security.svg)

Como uma abordagem alternativa e *segura* , os consumidores de contêineres de serviços cognitivas poderiam aumentar um contêiner com um componente front-end, mantendo o ponto de extremidade do contêiner privado. Vamos considerar um cenário em que usamos [İSTİO][istio] como um gateway de entrada. O İSTİO dá suporte a autenticação HTTPS/SSL e de certificado de cliente. Nesse cenário, o front-end İSTİO expõe o acesso ao contêiner, apresentando o certificado do cliente que está na lista de permissões antecipadamente com İSTİO.

[Nginx][nginx] é outra opção popular na mesma categoria. İSTİO e Nginx atuam como uma malha de serviço e oferecem recursos adicionais, incluindo coisas como balanceamento de carga, roteamento e controle de taxa.

### <a name="container-networking"></a>Funcionamento em rede do contentor

Os contêineres de serviços cognitivas são necessários para enviar informações de medição para fins de cobrança. A única exceção é os *contêineres offline* , pois eles seguem uma metodologia de cobrança diferente. Falha ao permitir listar vários canais de rede dos quais os contêineres de serviços cognitivas dependem impedirão que o contêiner funcione.

#### <a name="allow-list-cognitive-services-domains-and-ports"></a>Permitir listar domínios e portas de serviços cognitivas

O host deve permitir a **porta** de lista 443 e os seguintes domínios:

* `*.cognitive.microsoft.com`
* `*.cognitiveservices.azure.com`

#### <a name="disable-deep-packet-inspection"></a>Desabilitar inspeção profunda de pacote

> [Inspeção profunda de pacotes](https://en.wikipedia.org/wiki/Deep_packet_inspection) (DPI) é um tipo de processamento de dados que inspeciona em detalhes os dados que estão sendo enviados por uma rede de computadores e geralmente executa ações bloqueando, redirecionando ou registrando-os adequadamente.

Desabilite o DPI nos canais seguros que os contêineres de serviços cognitivas criam aos servidores da Microsoft. Se não for feito, isso impedirá que o contêiner funcione corretamente.

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../../security/fundamentals/overview.md
