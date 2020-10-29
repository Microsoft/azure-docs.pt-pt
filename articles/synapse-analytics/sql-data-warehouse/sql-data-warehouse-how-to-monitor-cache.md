---
title: Otimize a sua cache Gen2
description: Aprenda a monitorizar a sua cache Gen2 utilizando o portal Azure.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: fa5025e0a2bd260adeb23b4ab7c4d5f8bd83a43a
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "93026807"
---
# <a name="how-to-monitor-the-gen2-cache"></a>Como monitorizar a cache da Gen2

Este artigo descreve como monitorizar e resolver problemas de desempenho de consulta lenta, determinando se a sua carga de trabalho está a alavancar a cache gen2 de forma ótima.

A arquitetura de armazenamento Gen2 tiers automaticamente seus segmentos de loja de colunas mais frequentemente questionados em uma cache residente em SSDs baseados em NVMe projetados para armazéns de dados Gen2. Um maior desempenho é realizado quando as suas consultas recuperam segmentos que residem na cache.
 
## <a name="troubleshoot-using-the-azure-portal"></a>Resolução de problemas usando o portal Azure

Pode utilizar o Azure Monitor para visualizar as métricas de cache da Gen2 para resolver problemas de desempenho. Primeiro vá ao portal Azure e clique no **Monitor,** **Métricas** e **+ Selecione um âmbito** :

![Screenshot mostra Selecionar um âmbito selecionado a partir de Métricas no portal Azure.](./media/sql-data-warehouse-how-to-monitor-cache/cache-0.png)

Use as barras de pesquisa e de entrega para localizar o seu armazém de dados. Em seguida, selecione aplicar.

![A screenshot mostra o painel de âmbito Select, onde pode selecionar o seu armazém de dados.](./media/sql-data-warehouse-how-to-monitor-cache/cache-1.png)

As métricas-chave para a resolução de problemas da cache gen2 são a **percentagem de atingidos cache** e **a percentagem utilizada pela Cache.** Selecione **a percentagem de produção de Cache** e, em seguida, utilize o botão **métrico de adicionar** Cache para adicionar a **percentagem utilizada pela Cache** . 

![Métricas de Cache](./media/sql-data-warehouse-how-to-monitor-cache/cache-2.png)

## <a name="cache-hit-and-used-percentage"></a>Cache atingido e percentagem usada

A matriz abaixo descreve cenários baseados nos valores das métricas de cache:

|                                | **Alta percentagem de impacto de Cache** | **Baixa percentagem de impacto de Cache** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Alta percentagem utilizada em Cache** |          Cenário 1           |          Cenário 2          |
| **Baixa percentagem de cache usada**  |          Cenário 3           |          Cenário 4          |

**Cenário 1:** Está a utilizar o seu cache de forma ideal. [Resolva](sql-data-warehouse-manage-monitor.md) outros problemas que podem estar a abrandar as suas consultas.

**Cenário 2:** O seu conjunto de dados de trabalho atual não pode encaixar na cache, o que provoca uma baixa percentagem de impacto de cache devido a leituras físicas. Considere aumentar o seu nível de desempenho e reexame a sua carga de trabalho para preencher a cache.

**Cenário 3:** É provável que a sua consulta esteja a correr devagar devido a razões não relacionadas com a cache. [Resolva](sql-data-warehouse-manage-monitor.md) outros problemas que podem estar a abrandar as suas consultas. Também pode considerar [reduzir o seu caso](sql-data-warehouse-manage-monitor.md) para reduzir o tamanho do seu cache para economizar custos. 

**Cenário 4:** Tinhas uma cache fria que podia ser a razão pela qual a tua consulta era lenta. Considere a repetição da sua consulta, uma vez que o seu conjunto de dados de trabalho deve estar agora em cache. 

> [!IMPORTANT]
> Se a percentagem de cache ou cache utilizada não for atualizada após a reprodução da sua carga de trabalho, o seu conjunto de trabalho já pode estar a residir na memória. Apenas as mesas de loja de colunas agrupadas estão em cache.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a sintonização geral do desempenho da consulta, consulte [a execução da consulta do Monitor](sql-data-warehouse-manage-monitor.md#monitor-query-execution).
