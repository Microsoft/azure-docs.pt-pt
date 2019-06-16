---
title: Serviço de porta de entrada do Azure - monitorização de estado de funcionamento do back-end | Documentos da Microsoft
description: Este artigo ajuda-o a compreender como o serviço de porta de entrada do Azure monitoriza o estado de funcionamento do seu back-ends
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
ms.openlocfilehash: 59a3bac39437b91eeee3b005bd23476a34a308b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60736586"
---
# <a name="health-probes"></a>Sondas do estado de funcionamento

Para determinar o estado de funcionamento de cada back-end, cada ambiente de porta de entrada periodicamente envia um pedido HTTP/HTTPS sintético para cada um dos seus back-ends configurado. Depois, utiliza as respostas dessas sondas para determinar os “melhores” back-ends para os quais encaminhar os pedidos reais do cliente.


## <a name="supported-protocols"></a>Protocolos suportados

Porta de entrada suporta o envio de sondas através de protocolos HTTP ou HTTPS. Estas sondas são enviadas através das mesmas portas TCP configuradas para encaminhar pedidos do cliente e não podem ser anuladas.

## <a name="health-probe-responses"></a>Respostas de sonda de estado de funcionamento

| Responses  | Descrição | 
| ------------- | ------------- |
| Determinar o estado de funcionamento  |  Um código de 200 estado OK indica que o back-end está em bom estado. Tudo o resto é considerado uma falha. Se por algum motivo (incluindo a falha de rede), uma resposta HTTP válida não for recebida para uma sonda, a sonda é contada uma falha.|
| Medir a latência  | A latência é o tempo de relógio medido desde o momento imediatamente antes de podemos enviar o pedido de sonda para um momento quando recebermos o último byte de resposta. Utilizamos uma nova ligação de TCP para cada solicitação, para que esta medida não é viés back-ends com ligações de acesso pouco frequente existentes.  |

## <a name="how-front-door-determines-backend-health"></a>Como a porta da frente determina o estado de funcionamento do back-end

O serviço de porta de entrada do Azure utiliza o mesmo processo de três passos abaixo em todos os algoritmos para determinar o estado de funcionamento.

1. Back-ends de exclusão desabilitado.

2. Exclua o back-ends com erros de sondas de estado de funcionamento:
    * Esta seleção é feita ao ver o último _n_ nas respostas de sonda de estado de funcionamento. Se, pelo menos, _x_ estão em bom estado, o back-end é considerado em bom estado de funcionamento.

    * _n_ está configurado alterando a propriedade SampleSize nas definições de balanceamento de carga.

    * _x_ está configurado alterando a propriedade SuccessfulSamplesRequired nas definições de balanceamento de carga.

3. Fora o conjunto de back-ends de bom estado de funcionamento do conjunto de back-end, porta de entrada adicionalmente mede e mantém a latência de (tempo de ida e volta) de cada back-end.


## <a name="complete-health-probe-failure"></a>Falha de sonda de estado de funcionamento completo

Se falharem sondas de estado de funcionamento para cada back-end num conjunto de back-end, porta da frente considera todos os back-ends de bom estado de funcionamento e encaminha o tráfego numa distribuição round robin em todos os.

Depois de qualquer back-end devolve a um bom estado, porta da frente retomará o algoritmo de balanceamento de carga normal.

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
