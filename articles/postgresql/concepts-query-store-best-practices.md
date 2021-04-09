---
title: Query Store boas práticas na Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve as melhores práticas para a Loja de Consultas na Base de Dados Azure para PostgreSQL - Servidor Único.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: dd39b7ecd51902f5035b4cd17d59dea964d0c962
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91708837"
---
# <a name="best-practices-for-query-store"></a>Melhores práticas para a Loja de Consultas

**Aplica-se a:** Azure Database para PostgreSQL - Versões single Server 9.6, 10, 11

Este artigo descreve as melhores práticas para a utilização da Loja de Consultas na Base de Dados Azure para PostgreSQL.

## <a name="set-the-optimal-query-capture-mode"></a>Desa ajuste o modo de captura de consulta ideal
Deixe a Query Store capturar os dados que lhe interessam. 

|**pg_qs.consulta_capture_mode** | **Cenário**|
|---|---|
|_Todos_  |Analise cuidadosamente a sua carga de trabalho em termos de todas as consultas e suas frequências de execução e outras estatísticas. Identifique novas consultas na sua carga de trabalho. Detete se as consultas ad hoc forem usadas para identificar oportunidades para o utilizador ou parametrização automática. _Tudo_ vem com um aumento do custo de consumo de recursos. |
|_Início_  |Concentre a sua atenção em consultas de topo - as emitidas pelos clientes.
|_Nenhuma_ |Já capturou um conjunto de consultas e uma janela de tempo que quer investigar e quer eliminar as distrações que outras consultas podem introduzir. _Nenhum_ é adequado para ambientes de teste e marcação de bancada. _Nenhum_ deve ser usado com cuidado, pois pode perder a oportunidade de rastrear e otimizar novas consultas importantes. Não se pode recuperar dados das janelas do tempo passado. |

A Loja de Consultas também inclui uma loja para estatísticas de espera. Existe uma consulta adicional sobre o modo de captura que rege as estatísticas de espera: **pgms_wait_sampling.consulta_capture_mode** pode ser definido para _nenhum_ ou _para todos_. 

> [!NOTE] 
> **pg_qs.query_capture_mode** substitui **pgms_wait_sampling.query_capture_mode**. Se pg_qs.query_capture_mode não for _nenhum,_ a definição pgms_wait_sampling.query_capture_mode não tem qualquer efeito. 


## <a name="keep-the-data-you-need"></a>Guarde os dados de que precisa
O parâmetro **pg_qs.retenção_period_in_days** especifica em dias o período de retenção de dados para a Loja de Consultas. Os dados de consulta e estatística mais antigos são eliminados. Por predefinição, a Query Store está configurada para reter os dados durante 7 dias. Evite guardar dados históricos que não pretende utilizar. Aumente o valor se precisar de manter os dados por mais tempo.


## <a name="set-the-frequency-of-wait-stats-sampling"></a>Definir a frequência da amostragem de estatísticas de espera 
O parâmetro **pgms_wait_sampling.history_period** especifica a frequência (em milissegundos) de eventos de espera. Quanto mais curto o período, mais frequente a amostragem. Mais informação é recuperada, mas isso vem com o custo de um maior consumo de recursos. Aumente este período se o servidor estiver sob carga ou se não precisar da granularidade


## <a name="get-quick-insights-into-query-store"></a>Obtenha informações rápidas sobre a Loja de Consultas
Pode utilizar [o Insight de Desempenho de Consulta](concepts-query-performance-insight.md) no portal Azure para obter informações rápidas sobre os dados na Loja de Consultas. As visualizações surgem nas consultas de maior duração e nos eventos de espera mais longos ao longo do tempo.

## <a name="next-steps"></a>Passos seguintes
- Saiba como obter ou definir parâmetros utilizando o [portal Azure](howto-configure-server-parameters-using-portal.md) ou o [Azure CLI](howto-configure-server-parameters-using-cli.md).