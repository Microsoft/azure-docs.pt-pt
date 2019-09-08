---
title: Repositório de Consultas práticas recomendadas no banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve as práticas recomendadas para o Repositório de Consultas no banco de dados do Azure para PostgreSQL-servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 51239f4cf49784dd47470e1272b90508eaf25e6f
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764222"
---
# <a name="best-practices-for-query-store"></a>Práticas recomendadas para Repositório de Consultas

**Aplica-se a:** Banco de dados do Azure para PostgreSQL-versões de servidor único 9,6, 10, 11

Este artigo descreve as práticas recomendadas para usar Repositório de Consultas no banco de dados do Azure para PostgreSQL.

## <a name="set-the-optimal-query-capture-mode"></a>Definir o modo de captura de consulta ideal
Permita que Repositório de Consultas capture os dados que são importantes para você. 

|**pg_qs.query_capture_mode** | **Cenário**|
|---|---|
|_Todos_  |Analise sua carga de trabalho minuciosamente em termos de todas as consultas e de suas frequências de execução e outras estatísticas. Identifique novas consultas em sua carga de trabalho. Detectar se consultas ad hoc são usadas para identificar oportunidades para a parametrização automática ou de usuário. _Tudo_ vem com um maior custo de consumo de recursos. |
|_Início_  |Concentre sua atenção nas principais consultas – as emitidas pelos clientes.
|_Nenhum_ |Você já capturou um conjunto de consulta e uma janela de tempo que deseja investigar e deseja eliminar as distrações que outras consultas podem introduzir. _Nenhum_ é adequado para ambientes de teste e de marca de banca. _Nenhum_ deve ser usado com cautela, pois você pode perder a oportunidade de controlar e otimizar novas consultas importantes. Você não pode recuperar dados nessas janelas de tempo anteriores. |

Repositório de Consultas também inclui uma loja para estatísticas de espera. Há uma consulta de modo de captura adicional que governa estatísticas de espera: **pgms_wait_sampling. query_capture_mode** pode ser definido como _None_ ou _All_. 

> [!NOTE] 
> **pg_qs. query_capture_mode** substitui **pgms_wait_sampling. query_capture_mode**. Se pg_qs. query_capture_mode for _None_, a configuração pgms_wait_sampling. query_capture_mode não terá efeito. 


## <a name="keep-the-data-you-need"></a>Mantenha os dados necessários
O parâmetro **pg_qs. retention_period_in_days** especifica, em dias, o período de retenção de dados para repositório de consultas. Dados de consulta e estatísticas mais antigos são excluídos. Por padrão, o Repositório de Consultas é configurado para reter os dados por 7 dias. Evite manter os dados históricos que você não planeja usar. Aumente o valor se você precisar manter os dados mais longos.


## <a name="set-the-frequency-of-wait-stats-sampling"></a>Definir a frequência de amostragem de estatísticas de espera 
O parâmetro **pgms_wait_sampling. history_period** especifica com que frequência (em milissegundos) os eventos de espera são amostrados. Quanto menor o período, mais frequente é a amostragem. Mais informações são recuperadas, mas isso é fornecido com o custo de maior consumo de recursos. Aumentar esse período se o servidor estiver sob carga ou se você não precisar da granularidade


## <a name="get-quick-insights-into-query-store"></a>Obtenha informações rápidas sobre o Repositório de Consultas
Você pode usar [análise de desempenho de consultas](concepts-query-performance-insight.md) na portal do Azure para obter informações rápidas sobre os dados em repositório de consultas. As visualizações Surface as consultas mais longas e os eventos de espera mais longos ao longo do tempo.

## <a name="next-steps"></a>Passos Seguintes
- Saiba como obter ou definir parâmetros usando o [portal do Azure](howto-configure-server-parameters-using-portal.md) ou o [CLI do Azure](howto-configure-server-parameters-using-cli.md).