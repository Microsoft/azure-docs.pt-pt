---
title: Otimize a sua cache Gen2
description: Aprenda a monitorizar a sua cache Gen2 utilizando o portal Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: performance
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 924705b7ce1d324583077797714491bdf3fc5cc9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721222"
---
# <a name="how-to-monitor-the-gen2-cache"></a>Como monitorizar a cache gen2
A arquitetura de armazenamento Gen2 automaticamente nivete os seus segmentos de colunas mais frequentemente consultados numa cache residente em SSDs baseados em NVMe projetados para armazéns de dados Gen2. Um maior desempenho é realizado quando as suas consultas recuperam segmentos que residem na cache. Este artigo descreve como monitorizar e resolver problemas o desempenho da consulta lenta, determinando se a sua carga de trabalho está a aproveitar idealmente a cache gen2.  
## <a name="troubleshoot-using-the-azure-portal"></a>Resolução de problemas usando o portal Azure
Pode utilizar o Monitor Azure para ver as métricas de cache gen2 para resolver o desempenho da consulta. Primeiro vá ao portal Azure e clique no Monitor:

![Azure Monitor](./media/sql-data-warehouse-cache-portal/cache_0.png)

Selecione o botão métricas e preencha o grupo **de subscrição,** **grupo** **de recursos,** **tipo de recurso**e nome **de recurso** do seu armazém de dados.

As métricas-chave para a resolução de problemas da cache Gen2 são **a percentagem** de impacto cache e **a percentagem usada por Cache**. Configure o gráfico métrico Azure para exibir estas duas métricas.

![Métricas cache](./media/sql-data-warehouse-cache-portal/cache_1.png)


## <a name="cache-hit-and-used-percentage"></a>Cache hit e percentagem usada
A matriz abaixo descreve cenários baseados nos valores das métricas de cache:

|                                | **Alta percentagem de impacto de Cache** | **Baixa percentagem de impacto de Cache** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Alta cache de cache usada percentagem** |          Cenário 1           |          Cenário 2          |
| **Baixa cache usada percentagem**  |          Cenário 3           |          Cenário 4          |

**Cenário 1:** Está a usar o melhor da sua cache. [Problemas de resolução](sql-data-warehouse-manage-monitor.md) de outras áreas que podem estar a abrandar as suas consultas.

**Cenário 2:** O seu conjunto de dados de trabalho atual não pode caber na cache, o que provoca uma baixa percentagem de impacto em cache devido a leituras físicas. Considere aumentar o seu nível de desempenho e reexecutar a sua carga de trabalho para povoar a cache.

**Cenário 3:** É provável que a sua consulta esteja a decorrer lentamente devido a razões não relacionadas com a cache. [Problemas de resolução](sql-data-warehouse-manage-monitor.md) de outras áreas que podem estar a abrandar as suas consultas. Também pode considerar [reduzir a sua instância](sql-data-warehouse-manage-monitor.md) para reduzir o tamanho da cache para poupar custos. 

**Cenário 4:** Tinhas uma cache fria que podia ser a razão pela qual a tua consulta era lenta. Considere refazer a sua consulta, uma vez que o seu conjunto de dados de trabalho deve agora estar em cache. 

> [!IMPORTANT]
> Se a percentagem de impacto da cache ou a percentagem utilizada não estiver em atualização após a reexecução da sua carga de trabalho, o seu conjunto de trabalho já pode residir na memória. Apenas as mesas de lojas de colunas agrupadas estão em cache.

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre a sintonização geral do desempenho da consulta, consulte a [execução](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md#monitor-query-execution)da consulta do Monitor .
