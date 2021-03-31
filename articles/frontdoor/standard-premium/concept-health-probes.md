---
title: Azure Front Door Standard/Premium (Preview) Monitorização da sonda de saúde
description: Este artigo ajuda-o a entender como a Porta Frontal Azure monitoriza a saúde do seu backend.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 30a8208babab2991c9d9e86cc419ac50e1530d7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101100087"
---
# <a name="azure-front-door-standardpremium-preview-health-probe-monitoring"></a>Azure Front Door Standard/Premium (Preview) Monitorização da sonda de saúde

> [!Note]
> Esta documentação destina-se ao Azure Front Door Standard/Premium (Preview). À procura de informações sobre a Porta da Frente Azure? Ver [aqui](../front-door-overview.md).

A Azure Front Door envia periodicamente um pedido HTTP ou HTTPS a cada um dos seus backend. Estes pedidos permitem ao Azure Front Door determinar a saúde de cada ponto final na piscina de backend. A Porta da Frente utiliza então estas respostas da sonda para determinar os recursos de backend "melhores" para encaminhar os pedidos do seu cliente. 

> [!WARNING]
> Uma vez que a Porta Frontal tem muitos ambientes de borda a nível global, o volume da sonda de saúde para os seus backends pode ser bastante elevado - variando de 25 pedidos por minuto até 1200 pedidos por minuto, dependendo da frequência da sonda de saúde configurada. Com a frequência padrão da sonda de 30 segundos, o volume da sonda no seu backend deve ser de cerca de 200 pedidos por minuto.

## <a name="supported-protocols"></a>Protocolos suportados

A Porta Frontal suporta o envio de sondas através dos protocolos HTTP ou HTTPS. Estas sondas são enviadas através das mesmas portas TCP configuradas para encaminhar pedidos do cliente e não podem ser anuladas.

## <a name="supported-http-methods-for-health-probes"></a>Métodos HTTP suportados para sondas de saúde

Porta frontal suporta os seguintes métodos HTTP para o envio das sondas sanitárias:

* **GET:** O método GET significa recuperar qualquer informação (sob a forma de uma entidade) que seja identificada pelo Pedido-URI.
* **CABEÇA:** O método HEAD é idêntico ao GET exceto que o servidor NÃO DEVE devolver um corpo de mensagem na resposta. Para novos perfis da porta frontal, por padrão, o método da sonda é definido como HEAD.

> [!NOTE]
> Para uma carga mais baixa e um custo nas suas costas, a Porta Frontal recomenda a utilização de pedidos DE CABEÇA para sondas de saúde.

## <a name="health-probe-responses"></a>Respostas da sonda de saúde

| Respostas  | Description | 
| ------------- | ------------- |
| Determinação da Saúde  |  Um código de estado de 200 OK indica que o backend é saudável. Todo o resto é considerado um fracasso. Se, por qualquer motivo (incluindo falha de rede) não for recebida uma resposta HTTP válida para uma sonda, a sonda é contada como uma falha.|
| Medição da Latência  | A latência é o tempo do relógio de parede medido a partir do momento imediatamente antes de enviarmos o pedido da sonda para o momento em que recebemos o último byte da resposta. Usamos uma nova ligação TCP para cada pedido, por isso esta medição não é tendenciosa para backends com conexões quentes existentes.  |

## <a name="how-front-door-determines-backend-health"></a>Como a Porta da Frente determina a saúde do backend

Azure Front Door usa o mesmo processo de três etapas abaixo em todos os algoritmos para determinar a saúde.

1. Excluir backends desativado.

1. Excluir backends que tenham erros de sondas de saúde:

    * Esta seleção é feita olhando para as últimas respostas da sonda _de_ saúde. Se pelo menos _x_ são saudáveis, o backend é considerado saudável.

    * _n_ é configurado alterando a propriedade SampleSize em configurações de equilíbrio de carga.

    * _x_ é configurado alterando a propriedade SuccessfulSamplesRequired em configurações de equilíbrio de carga.

1. Para os conjuntos de backends saudáveis na piscina de backend, a Porta frontal mede e mantém a latência (tempo de ida e volta) para cada backend.


## <a name="complete-health-probe-failure"></a>Falha completa da sonda de saúde

Se as sondas de saúde falharem por cada backend em uma piscina de backend, então Front Door considera todos os backends saudáveis e encaminha o tráfego em uma distribuição de robin redondo em todos eles.

Uma vez que qualquer backend retorne a um estado saudável, então a Porta frontal retomará o algoritmo normal de equilíbrio de carga.

## <a name="disabling-health-probes"></a>Desativar sondas de saúde

Se tiver um único backend na piscina de backend ou apenas um backend estiver ativo numa piscina de backend, então pode optar por desativar as sondas de saúde. Ao fazê-lo, reduzirá a carga no backend da sua aplicação.

## <a name="next-steps"></a>Passos seguintes

Saiba como [criar um Padrão/Premium da Porta da Frente.](create-front-door-portal.md)
