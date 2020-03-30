---
title: Consultas Store boas práticas em Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve as melhores práticas para a Loja de Consultas em Base de Dados Azure para PostgreSQL - Servidor Único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 51239f4cf49784dd47470e1272b90508eaf25e6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70764222"
---
# <a name="best-practices-for-query-store"></a>Boas práticas para A Loja de Consultas

**Aplica-se a:** Base de Dados Azure para PostgreSQL - Versões Single Server 9.6, 10, 11

Este artigo descreve as melhores práticas para a utilização da Consulta Store na Base de Dados Azure para postgreSQL.

## <a name="set-the-optimal-query-capture-mode"></a>Detete o modo de captura de consulta ideal
Deixe a Consulta Store capturar os dados que lhe interessam. 

|**pg_qs.query_capture_mode** | **Cenário**|
|---|---|
|_Todos_  |Analise minuciosamente a sua carga de trabalho em termos de todas as consultas e das suas frequências de execução e outras estatísticas. Identifique novas consultas na sua carga de trabalho. Detete se as consultas ad hoc são usadas para identificar oportunidades para o utilizador ou parametrização automática. _Tudo_ vem com um aumento do custo de consumo de recursos. |
|_Parte Superior_  |Concentre a sua atenção nas principais consultas - as emitidas pelos clientes.
|_Nenhum_ |Já captou um conjunto de consultas e janelas temporais que quer investigar e quer eliminar as distrações que outras consultas podem introduzir. _Nenhum_ é adequado para ambientes de teste e marcação de bancos. _Nenhum_ deve ser usado com cautela, pois poderá perder a oportunidade de rastrear e otimizar novas consultas importantes. Não se pode recuperar dados das janelas do tempo passado. |

A Consulta Store também inclui uma loja para estatísticas de espera. Há uma consulta adicional de modo de captura que rege as estatísticas de espera: **pgms_wait_sampling.query_capture_mode** pode ser definida para _nenhum_ ou _para todos_. 

> [!NOTE] 
> **pg_qs.query_capture_mode** substitui **pgms_wait_sampling.query_capture_mode.** Se pg_qs.query_capture_mode _for nenhuma,_ a definição pgms_wait_sampling.query_capture_mode não tem qualquer efeito. 


## <a name="keep-the-data-you-need"></a>Guarde os dados de que necessita
O parâmetro **pg_qs.retention_period_in_days** especifica em dias o período de retenção de dados para a Loja de Consultas. Os dados de consulta e estatísticas mais antigos são eliminados. Por padrão, a Consulta Store está configurada para reter os dados durante 7 dias. Evite manter dados históricos que não pretende utilizar. Aumente o valor se precisar de manter os dados por mais tempo.


## <a name="set-the-frequency-of-wait-stats-sampling"></a>Desdefinir a frequência da amostragem de estatísticas de espera 
O parâmetro **pgms_wait_sampling.history_period** especifica a frequência (em milissegundos) eventos de espera são amostrados. Quanto mais curto o período, mais frequente é a amostragem. Mais informação é recuperada, mas isso vem com o custo de um maior consumo de recursos. Aumente este período se o servidor estiver sob carga ou se não precisar da granularidade


## <a name="get-quick-insights-into-query-store"></a>Obtenha informações rápidas sobre a Loja de Consultas
Pode utilizar o [Query Performance Insight](concepts-query-performance-insight.md) no portal Azure para obter informações rápidas sobre os dados na Consulta Store. As visualizações surgem nas consultas de execução mais longas e nos eventos de espera mais longos ao longo do tempo.

## <a name="next-steps"></a>Passos seguintes
- Aprenda a obter ou definir parâmetros utilizando o [portal Azure](howto-configure-server-parameters-using-portal.md) ou o [Azure CLI](howto-configure-server-parameters-using-cli.md).