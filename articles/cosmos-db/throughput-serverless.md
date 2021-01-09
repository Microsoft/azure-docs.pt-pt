---
title: Como escolher entre produção provisida e sem servidor no Azure Cosmos DB
description: Saiba como escolher entre produção provisionada e sem servidor para a sua carga de trabalho.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/08/2021
ms.openlocfilehash: 3f5c3400f319a3f9d5f1544457b009f90d479634
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98049835"
---
# <a name="how-to-choose-between-provisioned-throughput-and-serverless"></a>Como escolher entre produção provisão e sem servidor
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

A Azure Cosmos DB está disponível em dois modos de capacidade diferentes: [produção a provisionada](set-throughput.md) e [sem servidor](serverless.md). Pode executar exatamente as mesmas operações de base de dados em ambos os modos, mas a forma como é cobrado para estas operações é radicalmente diferente. O vídeo a seguir explica as diferenças fundamentais entre estes modos e como se encaixam em diferentes tipos de cargas de trabalho:

> [!VIDEO https://www.youtube.com/embed/CgYQo6uHyt0]

## <a name="detailed-comparison"></a>Comparação detalhada

| Critérios | Débito aprovisionado | Sem servidor |
| --- | --- | --- |
| Estado | Disponível em Geral | Em pré-visualização |
| Mais adequado para | Cargas de trabalho com tráfego sustentado que requerem desempenho previsível | Cargas de trabalho com tráfego intermitente ou imprevisível e baixo rácio de tráfego médio-máximo |
| Como funciona | Para cada um dos seus contentores, fornece alguma quantidade de produção expressa nas [Unidades de Pedido](request-units.md) por segundo. A cada segundo, este número de Unidades de Pedido está disponível para as suas operações de base de dados. A produção a provisionada pode ser atualizada manualmente ou ajustada automaticamente com [autoescala](provision-throughput-autoscale.md). | Execute as suas operações de base de dados contra os seus contentores sem ter de providenciar qualquer capacidade. |
| Geo-distribuição | Disponível (número ilimitado de regiões de Azure) | Indisponível (as contas sem servidor só podem ser executadas em 1 região do Azure) |
| Armazenamento máximo por contentor | Ilimitado | 50 GB |
| Desempenho | < 10 ms latência para leituras e escritos cobertos por SLA | < 10 ms latência para leituras pontuais e < 30 ms para escritas cobertas pela SLO |
| Modelo de faturação | A faturação é feita por hora para o R/S a provisionado, independentemente do número de RUs consumidos. | A faturação é feita por hora pela quantidade de RUs consumidas pelas suas operações de base de dados. |

> [!IMPORTANT]
> Algumas das limitações sem servidor podem ser facilitadas ou removidas quando o servidor não está disponível e **o seu feedback** nos ajudará a decidir! Contacte-nos e conte-nos mais sobre a sua experiência sem servidor: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

## <a name="estimating-your-expected-consumption"></a>Estimando o seu consumo esperado

Em algumas situações, pode não ser claro se o rendimento previsto ou sem servidor deve ser escolhido para uma determinada carga de trabalho. Para ajudar nesta decisão, pode estimar o seu consumo total **esperado,** que é o número total de RUs que pode consumir ao longo de um mês (pode estimar isso com a ajuda da tabela [aqui](plan-manage-costs.md#estimating-serverless-costs)mostrada)

**Exemplo 1**: espera-se que uma carga de trabalho rebente até um máximo de 500 RU/s e consuma um total de 20.000.000 RUs durante um mês.

- No modo de produção a provisionado, você providenciaria um recipiente com 500 RU/s por um custo mensal de: $0.008 * 5 * 730 = **$29,20**
- No modo sem servidor, pagaria pelas RUs consumidas: $0,25 * 20 = **$5,00**

**Exemplo 2**: espera-se que uma carga de trabalho rebente até um máximo de 500 RU/s e consuma um total de 250.000.000 RUs durante um mês.

- No modo de produção a provisionado, você providenciaria um recipiente com 500 RU/s por um custo mensal de: $0.008 * 5 * 730 = **$29,20**
- No modo sem servidor, pagaria pelos RUs consumidos: $0,25 * 250 = **$62,50**

(estes exemplos não são responsáveis pelo custo de armazenagem, que é o mesmo entre os dois modos)

> [!NOTE]
> Os custos apresentados no exemplo anterior são apenas para fins de demonstração. Consulte [a página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) para obter as últimas informações sobre preços.

## <a name="next-steps"></a>Passos seguintes

- Leia mais sobre [a produção de provisões na Azure Cosmos DB](set-throughput.md)
- Leia mais sobre [Azure Cosmos DB sem servidor](serverless.md)
- Familiarize-se com o conceito de [Unidades](request-units.md) de Pedido
