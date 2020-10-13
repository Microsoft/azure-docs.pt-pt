---
title: Segurança do contentor
titleSuffix: Azure Cognitive Services
description: Saiba como proteger o seu recipiente
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 348fb301d1a36c8df405c641f7644889417b11ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91545246"
---
## <a name="azure-cognitive-services-container-security"></a>Segurança do contentor Azure Cognitive Services

A segurança deve ser o principal foco sempre que estiver a desenvolver aplicações. A importância da segurança é uma métrica para o sucesso. Quando você está arquitetando uma solução de software que inclui recipientes de Serviços Cognitivos, é vital entender as limitações e capacidades disponíveis para você. Para obter mais informações sobre a segurança da rede, consulte [as redes virtuais Configure Azure Cognitive Services][az-security].

> [!IMPORTANT]
> Por *defeito, não* existe segurança no contentor dos Serviços Cognitivos API. A razão para isso é que, na maioria das vezes, o contentor funciona como parte de uma cápsula que é protegida do exterior por uma ponte de rede. No entanto, é possível permitir a autenticação que funciona de forma idêntica à autenticação utilizada no acesso aos [Serviços Cognitivos baseados na nuvem.][request-authentication]

O diagrama abaixo ilustra a abordagem padrão e **não segura:**

![Segurança do contentor](../media/container-security.svg)

Como uma abordagem alternativa e *segura,* os consumidores de recipientes de Serviços Cognitivos poderiam aumentar um recipiente com um componente frontal, mantendo o ponto final do contentor privado. Vamos considerar um cenário em que usamos [o Istio][istio] como porta de entrada. A Istio suporta HTTPS/TLS e autenticação de certificado de cliente. Neste cenário, o frontend istio expõe o acesso ao contentor, apresentando o certificado de cliente que é previamente aprovado com a Istio.

[Nginx][nginx] é outra escolha popular na mesma categoria. Tanto a Istio como a Nginx funcionam como uma malha de serviço e oferecem funcionalidades adicionais, incluindo coisas como o equilíbrio de carga, encaminhamento e controlo de tarifas.

### <a name="container-networking"></a>Funcionamento em rede do contentor

Os recipientes dos Serviços Cognitivos são obrigados a submeter informações de medição para efeitos de faturação. A única exceção é *os contentores offline,* uma vez que seguem uma metodologia de faturação diferente. A não identificação de vários canais de rede em que os contentores dos Serviços Cognitivos dependem impedirá o funcionamento do contentor.

#### <a name="allow-list-cognitive-services-domains-and-ports"></a>Permitir listar domínios e portas de Serviços Cognitivos

O anfitrião deve permitir a **lista da porta 443** e os seguintes domínios:

* `*.cognitive.microsoft.com`
* `*.cognitiveservices.azure.com`

#### <a name="disable-deep-packet-inspection"></a>Desativar a inspeção de pacotes profundos

> [A inspeção de pacotes profundos](https://en.wikipedia.org/wiki/Deep_packet_inspection) (DPI) é um tipo de processamento de dados que inspeciona detalhadamente os dados enviados através de uma rede de computadores, e geralmente toma medidas bloqueando, reencaminhando ou registando-os em conformidade.

Desative o DPI nos canais seguros que os contentores dos Serviços Cognitivos criam para servidores da Microsoft. Se não o fizer, o recipiente funcione corretamente.

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../cognitive-services-virtual-networks.md
