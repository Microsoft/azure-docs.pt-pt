---
title: Azure Front Door - monitorização de saúde backend / Microsoft Docs
description: Este artigo ajuda-o a entender como a Porta Frontal Azure monitoriza a saúde dos seus backends
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
ms.openlocfilehash: e2e656c395f1a31c1f5ebbd46d5a18a046f854f7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79471579"
---
# <a name="health-probes"></a>Sondas do estado de funcionamento

Para determinar a saúde e proximidade de cada backend de um determinado ambiente da Porta frontal, cada ambiente da Porta Frontal envia periodicamente um pedido de HTTP/HTTPS sintético a cada um dos seus backends configurados. Depois, utiliza as respostas dessas sondas para determinar os “melhores” back-ends para os quais encaminhar os pedidos reais do cliente. 

> [!WARNING]
> Uma vez que a Porta Frontal tem muitos ambientes de borda a nível global, a sonda de saúde solicita que o volume para os seus backends possa ser bastante elevado - variando de 25 pedidos por minuto até 1200 pedidos por minuto, dependendo da frequência da sonda de saúde configurada. Com a frequência padrão da sonda de 30 segundos, o volume da sonda no seu backend deve ser de cerca de 200 pedidos por minuto.

## <a name="supported-protocols"></a>Protocolos suportados

A Porta Frontal suporta o envio de sondas através dos protocolos HTTP ou HTTPS. Estas sondas são enviadas através das mesmas portas TCP configuradas para encaminhar pedidos do cliente e não podem ser anuladas.

## <a name="supported-http-methods-for-health-probes"></a>Métodos HTTP suportados para sondas de saúde

Porta frontal suporta os seguintes métodos HTTP para o envio das sondas sanitárias:

1. **GET:** O método GET significa recuperar qualquer informação (sob a forma de uma entidade) que seja identificada pelo Pedido-URI.
2. **CABEÇA:** O método HEAD é idêntico ao GET exceto que o servidor NÃO DEVE devolver um corpo de mensagem na resposta. Para novos perfis da porta frontal, por padrão, o método da sonda é definido como HEAD.

> [!NOTE]
> Para uma carga mais baixa e um custo nas suas costas, a Porta Frontal recomenda a utilização de pedidos DE CABEÇA para sondas de saúde.

## <a name="health-probe-responses"></a>Respostas da sonda de saúde

| Respostas  | Descrição | 
| ------------- | ------------- |
| Determinação da Saúde  |  Um código de estado de 200 OK indica que o backend é saudável. Todo o resto é considerado um fracasso. Se, por qualquer motivo (incluindo falha de rede) não for recebida uma resposta HTTP válida para uma sonda, a sonda é contada como uma falha.|
| Medição da Latência  | A latência é o tempo do relógio de parede medido a partir do momento imediatamente antes de enviarmos o pedido da sonda para o momento em que recebemos o último byte da resposta. Utilizamos uma nova ligação TCP para cada pedido, pelo que esta medição não é tendenciosa para backends com ligações quentes existentes.  |

## <a name="how-front-door-determines-backend-health"></a>Como a Porta da Frente determina a saúde do backend

Azure Front Door usa o mesmo processo de três etapas abaixo em todos os algoritmos para determinar a saúde.

1. Excluir backends desativado.

2. Excluir backends que tenham erros de sondas de saúde:
    * Esta seleção é feita olhando para as últimas respostas da sonda _de_ saúde. Se pelo menos _x_ são saudáveis, o backend é considerado saudável.

    * _n_ é configurado alterando a propriedade SampleSize em configurações de equilíbrio de carga.

    * _x_ é configurado alterando a propriedade SuccessfulSamplesRequired em configurações de equilíbrio de carga.

3. Fora do conjunto de backends saudáveis na piscina de backend, a Porta frontal mede adicionalmente e mantém a latência (tempo de ida e volta) para cada backend.


## <a name="complete-health-probe-failure"></a>Falha completa da sonda de saúde

Se as sondas de saúde falharem por cada backend em uma piscina de backend, então Front Door considera todos os backends saudáveis e encaminha o tráfego em uma distribuição de robin redondo em todos eles.

Uma vez que qualquer backend retorne a um estado saudável, então a Porta frontal retomará o algoritmo normal de equilíbrio de carga.

## <a name="disabling-health-probes"></a>Desativar sondas de saúde

Se tiver um único backend na sua piscina de backend, pode optar por desativar as sondas de saúde reduzindo a carga no backend da sua aplicação. Mesmo que tenha várias costas na piscina de backend, mas apenas um deles está em estado ativado, pode desativar as sondas de saúde.

## <a name="next-steps"></a>Próximos passos

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
