---
title: Serviço de porta frontal do Azure-monitoramento de integridade de back-end | Microsoft Docs
description: Este artigo ajuda você a entender como o serviço de porta frontal do Azure monitora a integridade dos back-ends
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 289b05a2c50a2b4af50eb2114515a49bb653cf1a
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742394"
---
# <a name="health-probes"></a>Sondas de estado de funcionamento

Para determinar a integridade de cada back-end, cada ambiente de porta de entrada periodicamente envia uma solicitação de HTTP/HTTPS sintéticas para cada um dos seus front-ends configurados. Depois, utiliza as respostas dessas sondas para determinar os “melhores” back-ends para os quais encaminhar os pedidos reais do cliente. Observe que, como a porta frontal tem muitos ambientes de borda globalmente, o volume de solicitações de investigação de integridade para seus back-ends pode ser tão alto quanto mais de uma solicitação por segundo depende da frequência de investigação de integridade configurada. 



## <a name="supported-protocols"></a>Protocolos suportados

A porta frontal dá suporte ao envio de investigações por meio de protocolos HTTP ou HTTPS. Estas sondas são enviadas através das mesmas portas TCP configuradas para encaminhar pedidos do cliente e não podem ser anuladas.

## <a name="health-probe-responses"></a>Respostas de investigação de integridade

| Responses  | Descrição | 
| ------------- | ------------- |
| Determinando a integridade  |  Um código de status de 200 OK indica que o back-end está íntegro. Tudo o mais é considerado uma falha. Se por qualquer motivo (incluindo falha de rede) uma resposta HTTP válida não for recebida para uma investigação, a investigação será contada como uma falha.|
| Medindo a latência  | Latência é o tempo de relógio de parede medido desde o momento imediatamente antes de enviarmos a solicitação de investigação ao momento em que recebemos o último byte da resposta. Usamos uma nova conexão TCP para cada solicitação, portanto, essa medida não é ajustada em relação aos back-ends com conexões passivas existentes.  |

## <a name="how-front-door-determines-backend-health"></a>Como a porta frontal determina a integridade do back-end

O serviço de porta frontal do Azure usa o mesmo processo de três etapas abaixo em todos os algoritmos para determinar a integridade.

1. Excluir back-ends desabilitados.

2. Exclua os back-ends que têm erros de investigação de integridade:
    * Essa seleção é feita examinando as últimas _n_ respostas de investigação de integridade. Se pelo menos _x_ estiverem íntegros, o back-end será considerado íntegro.

    * _n_ é configurado alterando a propriedade Samples em configurações de balanceamento de carga.

    * _x_ é configurado alterando a propriedade SuccessfulSamplesRequired nas configurações de balanceamento de carga.

3. Fora do conjunto de back-ends íntegros no pool de back-end, a porta da frente também mede e mantém a latência (tempo de ida e volta) para cada back-end.


## <a name="complete-health-probe-failure"></a>Falha na investigação de integridade completa

Se as investigações de integridade falharem para cada back-end em um pool de back-end, a porta da frente considerará todos os front-ends íntegros e roteará o tráfego em uma distribuição de round robin entre todos eles.

Depois que qualquer back-end retornar a um estado íntegro, a porta da frente retomará o algoritmo normal de balanceamento de carga.

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
