---
title: Como escolher entre produção provisida e sem servidor no Azure Cosmos DB
description: Saiba como escolher entre produção provisionada e sem servidor para a sua carga de trabalho.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: d16343864d9602d644b31d34a2b66e39211b6ece
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93079344"
---
# <a name="how-to-choose-between-provisioned-throughput-and-serverless"></a>Como escolher entre produção provisão e sem servidor
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

A Azure Cosmos DB está disponível em dois modos de capacidade diferentes: [produção a provisionada](set-throughput.md) e [sem servidor](serverless.md). Pode executar exatamente as mesmas operações de base de dados em ambos os modos, mas a forma como é cobrado para estas operações é radicalmente diferente. O vídeo a seguir explica as diferenças fundamentais entre estes modos e como se encaixam em diferentes tipos de cargas de trabalho:

> [!VIDEO https://www.youtube.com/embed/CgYQo6uHyt0]

> [!NOTE]
> Atualmente, o Serverless é suportado apenas na API do Azure Cosmos DB Core (SQL).

## <a name="detailed-comparison"></a>Comparação detalhada

| Critérios | Débito aprovisionado | Sem servidor |
| --- | --- | --- |
| Estado | Disponível em Geral | Em pré-visualização |
| Mais adequado para | Cargas de trabalho críticas à missão que requerem um desempenho previsível | Cargas de trabalho não críticas pequenas a médias com tráfego de luz |
| Como funciona | Para cada um dos seus contentores, fornece alguma quantidade de produção expressa nas [Unidades de Pedido](request-units.md) por segundo. A cada segundo, este número de Unidades de Pedido está disponível para as suas operações de base de dados. A produção a provisionada pode ser atualizada manualmente ou ajustada automaticamente com [autoescala](provision-throughput-autoscale.md). | Execute as suas operações de base de dados contra os seus contentores sem ter de providenciar qualquer capacidade. |
| Limitações por conta | Número máximo de regiões de Azure: ilimitadas | Número máximo de regiões de Azure: 1 |
| Limitações por contentor | Produção máxima: ilimitada<br>Armazenamento máximo: ilimitado | Produção máxima: 5.000 RU/s<br>Armazenamento máximo: 50 GB |
| Desempenho | 99,99% para 99,999% disponibilidade coberta pela SLA<br>< 10 ms latência para leituras e escritos cobertos por SLA<br>Produção garantida de 99,99% abrangida pelo SLA | Disponibilidade de 99,9% a 99,99% abrangida pela SLA<br>< 10 ms latência para leituras pontuais e < 30 ms para escritas cobertas pela SLO<br>95% de burstability coberto pela SLO |
| Modelo de faturação | A faturação é feita por hora para o R/S a provisionado, independentemente do número de RUs consumidos. | A faturação é feita por hora pela quantidade de RUs consumidas pelas suas operações de base de dados. |

> [!IMPORTANT]
> Algumas das limitações sem servidor podem ser facilitadas ou removidas quando o servidor não está disponível e **o seu feedback** nos ajudará a decidir! Contacte-nos e conte-nos mais sobre a sua experiência sem servidor: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

## <a name="burstability-and-expected-consumption"></a>Burstability e consumo esperado

Em algumas situações, pode não ser claro se o rendimento previsto ou sem servidor deve ser escolhido para uma determinada carga de trabalho. Para ajudar nesta decisão, pode estimar:

- O requisito de **burstability** da sua carga de trabalho, é essa a quantidade máxima de RUs que pode precisar para consumir num segundo
- O seu **consumo total esperado,** é esse o número total de RUs que pode consumir ao longo de um mês (pode estimar isso com a ajuda da tabela [aqui](plan-manage-costs.md#estimating-serverless-costs)mostrada)

Se a sua carga de trabalho necessitar de explodir acima de 5.000 RU por segundo, a produção a provisionada deve ser escolhida porque os recipientes sem servidor não podem rebentar acima deste limite. Caso contrário, pode comparar o custo de ambos os modos com base no seu consumo esperado.

**Exemplo 1** : espera-se que uma carga de trabalho rebente até um máximo de 10.000 RU/s e consuma um total de 20.000.000 RUs durante um mês.

- Apenas o modo de produção a provisionado pode entregar uma produção de 10.000 RU/s

**Exemplo 2** : espera-se que uma carga de trabalho rebente até um máximo de 500 RU/s e consuma um total de 20.000.000 RUs durante um mês.

- No modo de produção a provisionado, você providenciaria um recipiente com 500 RU/s por um custo mensal de: $0.008 * 5 * 730 = **$29,20**
- No modo sem servidor, pagaria pelas RUs consumidas: $0,25 * 20 = **$5,00**

**Exemplo 3** : espera-se que uma carga de trabalho rebente até um máximo de 500 RU/s e consuma um total de 250.000.000 RUs durante um mês.

- No modo de produção a provisionado, você providenciaria um recipiente com 500 RU/s por um custo mensal de: $0.008 * 5 * 730 = **$29,20**
- No modo sem servidor, pagaria pelos RUs consumidos: $0,25 * 250 = **$62,50**

(estes exemplos não são responsáveis pelo custo de armazenagem, que é o mesmo entre os dois modos)

> [!NOTE]
> Os custos apresentados no exemplo anterior são apenas para fins de demonstração. Consulte [a página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) para obter as últimas informações sobre preços.

## <a name="next-steps"></a>Passos seguintes

- Leia mais sobre [a produção de provisões na Azure Cosmos DB](set-throughput.md)
- Leia mais sobre [Azure Cosmos DB sem servidor](serverless.md)
- Familiarize-se com o conceito de [Unidades](request-units.md) de Pedido
