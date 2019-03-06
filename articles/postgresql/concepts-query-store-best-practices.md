---
title: Práticas recomendadas do Store de consulta na base de dados do Azure para PostgreSQL
description: Este artigo descreve as melhores práticas para o Store de consulta na base de dados do Azure para PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: c4fcdc43e8c88bf307e4de5727df0641616d7b78
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57448140"
---
# <a name="best-practices-for-query-store"></a>Melhores práticas para Store de consulta

**Aplica-se a:** Base de dados do Azure para PostgreSQL 9.6 e 10

> [!IMPORTANT]
> A funcionalidade de consulta Store está em pré-visualização pública.


Este artigo descreve as melhores práticas para utilizar Store de consulta na base de dados do Azure para PostgreSQL.

## <a name="set-the-optimal-query-capture-mode"></a>Definir o modo de captura ideal de consultas
Store de consulta permitem capturar os dados que é importante para si. 

|**pg_qs.query_capture_mode** | **Cenário**|
|---|---|
|_Todos_  |Analise a carga de trabalho minuciosamente em termos de todas as consultas e respetivas frequências de execução e outras estatísticas. Identifica novas consultas na carga de trabalho. Detete se as consultas ad hoc são utilizadas para identificar as oportunidades de utilizador ou de parametrização automática. _Todos os_ vem com um consumo de recursos maior de custos. |
|_Top_  |Se concentre sua atenção em consultas principais - os emitidos pelos clientes.
|_Nenhum_ |Já foram capturadas um conjunto de consulta e a janela de tempo que pretende investigar e quiser eliminar as distrações que podem apresentar outras consultas. _Nenhum_ é adequado para teste e avaliar comparativamente ambientes. _Nenhum_ deve ser utilizada com cuidado, pois pode perder a oportunidade de controlar e otimizar as consultas de novo importantes. Não é possível recuperar dados nesses últimos janelas de tempo. |

Consulta Store também inclui um arquivo para estatísticas de espera. Existe uma consulta de modo de captura adicionais que rege as estatísticas de espera: **pgms_wait_sampling.query_capture_mode** pode ser definido como _none_ ou _todos os_. 

> [!NOTE] 
> **pg_qs.query_capture_mode** substitui **pgms_wait_sampling.query_capture_mode**. Se for pg_qs.query_capture_mode _none_, a definição de pgms_wait_sampling.query_capture_mode não tem qualquer efeito. 


## <a name="keep-the-data-you-need"></a>Manter os dados que necessários
O **pg_qs.retention_period_in_days** parâmetro especifica em dias do período de retenção de dados para consulta Store. Dados de consulta e as estatísticas mais antigos serão eliminados. Por predefinição, a consulta Store está configurado para manter os dados durante sete dias. Evite a manter os dados históricos que não planeia utilizar. Se precisar de manter os dados mais tempo, aumente o valor.


## <a name="set-the-frequency-of-wait-stats-sampling"></a>Defina a frequência de amostragem de estatísticas de espera 
O **pgms_wait_sampling.history_period** parâmetro especifica a frequência (em milissegundos) são recolhidos os eventos de espera. Quanto menor o período, quanto mais frequentes a amostragem. Obter mais informações são obtidas, mas o que é fornecido com o custo de maior consumo de recursos. Aumentar este período, se o servidor está sob carga ou não é necessário a granularidade


## <a name="get-quick-insights-into-query-store"></a>Consiga informações rápidas sobre Store de consulta
Pode usar [Query Performance Insight](concepts-query-performance-insight.md) no portal do Azure para obter informações rápidas sobre os dados na consulta Store. As visualizações a execução de consultas mais longo de superfície e há mais tempo de espera eventos ao longo do tempo.

## <a name="next-steps"></a>Passos Seguintes
- Saiba como obter ou definir os parâmetros a utilizar o [portal do Azure](howto-configure-server-parameters-using-portal.md) ou o [CLI do Azure](howto-configure-server-parameters-using-cli.md).