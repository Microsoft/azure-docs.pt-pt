---
title: Segurança do contentor
titleSuffix: Azure Cognitive Services
description: Aprenda a proteger o seu recipiente
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: fd2a6cdad01302501e30ec60a4d3ccf6efd9c266
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876839"
---
## <a name="azure-cognitive-services-container-security"></a>Segurança de contentores dos Serviços Cognitivos Azure

A segurança deve ser um foco principal sempre que estiver a desenvolver aplicações. A importância da segurança é uma métrica para o sucesso. Quando se está a arquitetar uma solução de software que inclui recipientes de Serviços Cognitivos, é vital compreender as limitações e capacidades de que dispomos. Para obter mais informações sobre segurança da rede, consulte as redes virtuais do [Configure Azure Cognitive Services.][az-security]

> [!IMPORTANT]
> Por *defeito, não* existe segurança no contentor dos Serviços Cognitivos API. A razão para isso é que, na maioria das vezes, o contentor funcionará como parte de uma cápsula que é protegida do exterior por uma ponte de rede. No entanto, é possível ativar a autenticação que funciona de forma idêntica à autenticação utilizada no acesso aos [Serviços Cognitivos baseados na nuvem.][request-authentication]

O diagrama abaixo ilustra a abordagem predefinida e **não segura:**

![Segurança do contentor](../media/container-security.svg)

Como uma abordagem alternativa e *segura,* os consumidores de recipientes de Serviços Cognitivos poderiam aumentar um recipiente com um componente frontal, mantendo o ponto final do recipiente privado. Vamos considerar um cenário em que usamos [istio][istio] como um portal de entrada. A Istio suporta https/TLS e autenticação de certificado de cliente. Neste cenário, o frontend istio expõe o acesso ao contentor, apresentando o certificado de cliente que é previamente listado com istio.

[Nginx][nginx] é outra escolha popular na mesma categoria. Tanto istio como Nginx atuam como uma malha de serviço e oferecem funcionalidades adicionais, incluindo coisas como o equilíbrio de carga, encaminhamento e controlo de tarifas.

### <a name="container-networking"></a>Funcionamento em rede do contentor

Os recipientes dos Serviços Cognitivos são obrigados a apresentar informações de medição para efeitos de faturação. A única exceção é os *contentores offline,* uma vez que seguem uma metodologia de faturação diferente. A não estão a permitir a lista de vários canais de rede em que os contentores dos Serviços Cognitivos dependem impedirão o funcionamento do contentor.

#### <a name="allow-list-cognitive-services-domains-and-ports"></a>Permitir lista de domínios e portas de Serviços Cognitivos

O anfitrião deve permitir a **porta 443** e os seguintes domínios:

* `*.cognitive.microsoft.com`
* `*.cognitiveservices.azure.com`

#### <a name="disable-deep-packet-inspection"></a>Desativar a inspeção de pacotes profundos

> [A inspeção de pacotes profundos](https://en.wikipedia.org/wiki/Deep_packet_inspection) (DPI) é um tipo de processamento de dados que inspeciona detalhadamente os dados que são enviados através de uma rede informática, e geralmente toma medidas bloqueando, reencaminhando ou registando-os em conformidade.

Desative o DPI nos canais seguros que os recipientes dos Serviços Cognitivos criam para os servidores da Microsoft. Se não o fizer, impedirá o funcionamento correto do recipiente.

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../cognitive-services-virtual-networks.md
