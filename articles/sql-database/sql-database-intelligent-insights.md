---
title: Monitorar o desempenho do banco de dados com Intelligent Insights-banco de dados SQL do Azure
description: O banco de dados SQL do Azure Intelligent Insights usa inteligência interna para monitorar continuamente o uso do banco de dados por meio de inteligência artificial e detectar eventos de interrupção que causam baixo desempenho.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 12/19/2018
ms.openlocfilehash: c073b535271ba8f9f11e17ba5203b13e257e211b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689690"
---
# <a name="intelligent-insights-using-ai-to-monitor-and-troubleshoot-database-performance"></a>Intelligent Insights usando o ia para monitorar e solucionar problemas de desempenho do banco de dados

O banco de dados SQL do Azure Intelligent Insights permite saber o que está acontecendo com o banco de dados SQL e o desempenho do banco de dados Instância Gerenciada.

O Intelligent Insights usa inteligência interna para monitorar continuamente o uso do banco de dados por meio de inteligência artificial e detectar eventos de interrupção que causam baixo desempenho. Depois de detectada, é executada uma análise detalhada que gera um log de diagnóstico com uma avaliação inteligente do problema. Essa avaliação consiste em uma análise da causa raiz do problema de desempenho do banco de dados e, quando possível, recomendações para melhorias de desempenho.

## <a name="what-can-intelligent-insights-do-for-you"></a>O que Intelligent Insights pode fazer por você

Intelligent Insights é um recurso exclusivo da inteligência interna do Azure que fornece o seguinte valor:

- Monitorização proativa
- Informações de desempenho personalizadas
- Detecção antecipada da degradação do desempenho do banco de dados
- Análise da causa raiz dos problemas detectados
- Recomendações de melhoria de desempenho
- Capacidade de expansão em centenas de milhares de bancos de dados
- Impacto positivo nos recursos DevOps e no custo total de propriedade

## <a name="how-does-intelligent-insights-work"></a>Como funciona Intelligent Insights

Intelligent Insights analisa o desempenho do banco de dados comparando a carga de trabalho do banco de dados da última hora com a carga de trabalho de linha de base de sete dias A carga de trabalho do banco de dados é composta de consultas determinadas para ser a mais significativa para o desempenho do banco de dados, como as consultas mais repetidas e maiores. Uma vez que cada banco de dados é exclusivo com base em sua estrutura, data, uso e aplicativo, cada linha de base de carga de trabalho gerada é específica e exclusiva para uma instância individual. Intelligent Insights, independentemente da linha de base da carga de trabalho, também monitora limites operacionais absolutos e detecta problemas com tempos de espera excessivos, exceções críticas e problemas com parametrizações de consulta que podem afetar o desempenho.

Após a detecção de um problema de degradação de desempenho de várias métricas observadas usando inteligência artificial, a análise é executada. Um log de diagnóstico é gerado com um insight inteligente sobre o que está acontecendo com seu banco de dados. Intelligent Insights facilita o rastreamento do problema de desempenho do banco de dados de sua primeira aparência até a resolução. Cada problema detectado é controlado por meio de seu ciclo de vida da detecção inicial de problemas e da verificação da melhoria do desempenho até sua conclusão.

![Fluxo de trabalho de análise de desempenho de banco](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)

As métricas usadas para medir e detectar problemas de desempenho de banco de dados baseiam-se na duração da consulta, solicitações de tempo limite, tempos de espera excessivos e solicitações com erros. Para obter mais informações sobre métricas, consulte a seção [métricas de detecção](sql-database-intelligent-insights.md#detection-metrics) deste documento.

As degradações de desempenho do banco de dados SQL identificadas são registradas no log de diagnóstico com entradas inteligentes que consistem nas seguintes propriedades:

| Propriedade             | Detalhes              |
| :------------------- | ------------------- |
| Informações do banco de dados | Metadados sobre um banco de dados no qual uma informação foi detectada, como um URI de recurso. |
| Intervalo de tempo observado | Hora de início e término do período da Insight detectada. |
| Métricas impactadas | Métricas que causaram a geração de uma percepção: <ul><li>Aumento da duração da consulta [segundos].</li><li>Espera excessiva [segundos].</li><li>Solicitações de tempo limite [percentual].</li><li>Solicitações com erro [percentual].</li></ul>|
| Valor de impacto | Valor de uma métrica medida. |
| Consultas afetadas e códigos de erro | Hash de consulta ou código de erro. Eles podem ser usados para correlacionar facilmente a consultas afetadas. São fornecidas métricas que consistem em aumento da duração da consulta, tempo de espera, contagens de tempo limite ou códigos de erro. |
| Detecções | Detecção identificada no banco de dados durante o tempo de um evento. Há 15 padrões de detecção. Para obter mais informações, consulte [solucionar problemas de desempenho do banco de dados com o Intelligent insights](sql-database-intelligent-insights-troubleshoot-performance.md). |
| Análise da origem do problema | Análise da causa raiz do problema identificado em um formato legível por humanos. Algumas informações podem conter uma recomendação de melhoria de desempenho sempre que possível. |
|||

Para obter uma visão geral prática sobre como usar Intelligent Insights com Análise de SQL do Azure e para cenários de uso típicos, consulte o vídeo incorporado:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

Intelligent Insights se destaca na descoberta e solução de problemas de desempenho do banco de dados SQL. Para usar Intelligent Insights para solucionar problemas de desempenho de banco de dados SQL e Instância Gerenciada de banco de dados, consulte [solucionar problemas de desempenho do banco de dados SQL do Azure com Intelligent insights](sql-database-intelligent-insights-troubleshoot-performance.md).

## <a name="configure-intelligent-insights"></a>Configurar Intelligent Insights

A saída do Intelligent Insights é um log de diagnóstico de desempenho inteligente. Esse log pode ser consumido de várias maneiras, transmitindo-o para Análise de SQL do Azure, hubs de eventos do Azure e armazenamento do Azure ou um produto de terceiros.

- Use o produto com [análise de SQL do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) para exibir informações por meio da interface do usuário do portal do Azure. Essa é a solução integrada do Azure e a maneira mais comum de exibir informações.
- Use o produto com os hubs de eventos do Azure para o desenvolvimento de cenários personalizados de monitoramento e alertas
- Use o produto com o armazenamento do Azure para o desenvolvimento de aplicativos personalizados, como, por exemplo, relatórios personalizados, arquivamento de dados de longo prazo e assim por diante.

A integração do Intelligent Insights com outros produtos Análise de SQL do Azure, o Hub de eventos do Azure, o armazenamento do Azure ou produtos de terceiros para consumo é realizada pela primeira vez habilitando o registro em log Intelligent Insights (o log "sqlsights") no diagnóstico a folha configurações de um banco de dados e, em seguida, a configuração de Intelligent Insights log a ser transmitida para um desses produtos.

Para obter mais informações sobre como habilitar o registro em log de Intelligent Insights e configurar os dados de log para serem transmitidos para um produto de consumo, consulte [log de diagnóstico e métricas do Azure SQL Database](sql-database-metrics-diag-logging.md).

### <a name="set-up-with-azure-sql-analytics"></a>Configurar com Análise de SQL do Azure

A solução de Análise de SQL do Azure fornece recursos de interface gráfica do usuário, relatórios e alertas sobre o desempenho do banco de dados, juntamente com os Intelligent Insights de log de diagnóstico.

> [!TIP]
> Introdução rápida: a maneira mais fácil de começar a usar o Intelligent Insights é usá-lo junto com Análise de SQL do Azure que fornecerá uma interface gráfica do usuário a problemas de desempenho do banco de dados. Adicione Análise de SQL do Azure solução do Marketplace, crie um espaço de trabalho dentro dessa solução e, em seguida, para cada banco de dados no qual você deseja habilitar Intelligent Insights, configure o streaming do log "sqlsights" na folha configurações de diagnóstico de um banco de dados para o espaço de trabalho de Análise de SQL do Azure.
>

O pré-requisito é ter Análise de SQL do Azure adicionado ao seu painel de portal do Azure do Marketplace e para criar um espaço de trabalho, consulte [configurar análise de SQL do Azure](../azure-monitor/insights/azure-sql.md#configuration)

Para usar Intelligent Insights com Análise de SQL do Azure, configure Intelligent Insights dados de log a serem transmitidos para Análise de SQL do Azure espaço de trabalho que você criou na etapa anterior, consulte [log de diagnóstico e métricas do banco de dados SQL do Azure](sql-database-metrics-diag-logging.md).

O exemplo a seguir mostra um Intelligent Insights exibido por meio de Análise de SQL do Azure:

![Relatório de Intelligent Insights](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

### <a name="set-up-with-event-hubs"></a>Configurar com hubs de eventos

Para usar Intelligent Insights com os hubs de eventos, configure Intelligent Insights dados de log a serem transmitidos para os hubs de eventos, consulte [transmitir logs de diagnóstico do Azure para os hubs de eventos](../azure-monitor/platform/resource-logs-stream-event-hubs.md).

Para usar os hubs de eventos para configurar o monitoramento e alertas personalizados, consulte [o que fazer com métricas e logs de diagnóstico em hubs de eventos](sql-database-metrics-diag-logging.md#what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs).

### <a name="set-up-with-azure-storage"></a>Configurar com o armazenamento do Azure

Para usar Intelligent Insights com armazenamento, configure Intelligent Insights dados de log a serem transmitidos para armazenamento, consulte [transmitir para o armazenamento do Azure](sql-database-metrics-diag-logging.md#stream-into-storage).

### <a name="custom-integrations-of-intelligent-insights-log"></a>Integrações personalizadas do log do Intelligent Insights

Para usar Intelligent Insights com ferramentas de terceiros ou para o desenvolvimento de alertas personalizados e monitoramento, consulte [usar o log de diagnóstico de desempenho do banco de dados Intelligent insights](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="detection-metrics"></a>Métricas de detecção

As métricas usadas para modelos de detecção que geram Intelligent Insights se baseiam no monitoramento:

- Duração da consulta
- Solicitações de tempo limite
- Tempo de espera excessivo
- Solicitações com erro

As solicitações duração da consulta e tempo limite são usadas como modelos primários na detecção de problemas com o desempenho da carga de trabalho do banco de dados. Eles são usados porque medem diretamente o que está acontecendo com a carga de trabalho. Para detectar todos os casos possíveis de degradação do desempenho da carga de trabalho, o tempo de espera excessivo e as solicitações com erros são usados como modelos adicionais para indicar problemas que afetam o desempenho da carga de trabalho.

O sistema automaticamente considera as alterações na carga de trabalho e altera o número de solicitações de consulta feitas ao banco de dados para determinar dinamicamente os limites de desempenho normal e fora do comum do banco de dados.

Todas as métricas são consideradas juntas em várias relações por meio de um modelo de dados derivado de forma científica que categoriza cada problema de desempenho detectado. As informações fornecidas por meio de um insight inteligente incluem:

- Detalhes do problema de desempenho detectado.
- Uma análise da causa raiz do problema detectado.
- Recomendações sobre como melhorar o desempenho do banco de dados SQL monitorado, sempre que possível.

## <a name="query-duration"></a>Duração da consulta

O modelo de degradação da duração da consulta analisa consultas individuais e detecta o aumento no tempo necessário para compilar e executar uma consulta em comparação com a linha de base de desempenho.

Se a inteligência interna do banco de dados SQL detectar um aumento significativo na compilação da consulta ou no tempo de execução da consulta que afeta o desempenho da carga de trabalho, essas consultas serão sinalizadas como problemas de degradação do desempenho da duração da consulta.

O log de diagnóstico Intelligent Insights gera o hash de consulta da consulta degradada no desempenho. O hash de consulta indica se a degradação do desempenho estava relacionada ao aumento do tempo de execução ou da compilação da consulta, o que aumentou o tempo de duração da consulta.

## <a name="timeout-requests"></a>Solicitações de tempo limite

O modelo de degradação de solicitações de tempo limite analisa consultas individuais e detecta qualquer aumento nos tempos limite no nível de execução da consulta e os tempos limite de solicitação geral no nível do banco de dados em comparação com o período de linha de base de desempenho.

Algumas das consultas podem atingir o tempo limite até mesmo antes que cheguem ao estágio de execução. Por meio de trabalhadores anulados versus solicitações feitas, a inteligência interna do banco de dados SQL mede e analisa todas as consultas que atingiram o banco de dados, independentemente de terem sido realizadas no estágio de execução ou não.

Após o número de tempos limite para consultas executadas ou o número de trabalhos de solicitação anulados cruzar o limite gerenciado pelo sistema, um log de diagnóstico é populado com informações inteligentes.

As informações geradas contêm o número de solicitações que atingiram o tempo limite e o número de consultas expiradas. A indicação da degradação do desempenho está relacionada ao aumento do tempo limite no estágio de execução ou o nível geral do banco de dados é fornecido. Quando o aumento nos tempos limite é considerado significativo para o desempenho do banco de dados, essas consultas são sinalizadas como problemas de degradação de desempenho de tempo limite.

## <a name="excessive-wait-times"></a>Tempos de espera excessivos

O modelo de tempo de espera excessivo monitora consultas de banco de dados individuais. Ele detecta estatísticas de espera de consulta excepcionalmente altas que cruzam os limites absolutos gerenciados pelo sistema. As métricas de tempo de espera excessivas de consulta a seguir são observadas usando o novo recurso SQL Server, Repositório de Consultas aguardar estatísticas (sys. query_store_wait_stats):

- Atingir limites de recursos
- Alcançando limites de recursos do pool elástico
- Número excessivo de threads de trabalho ou sessão
- Bloqueio excessivo de banco de dados
- Pressão de memória
- Outras estatísticas de espera

Atingir limites de recursos ou limites de recursos de pool elástico denotam que o consumo de recursos disponíveis em uma assinatura ou no pool elástico ultrapassou os limites absolutos. Essas estatísticas indicam a degradação do desempenho da carga de trabalho. Um número excessivo de threads de trabalho ou sessão denota uma condição em que o número de threads de trabalho ou sessões iniciadas ultrapassou os limites absolutos. Essas estatísticas indicam a degradação do desempenho da carga de trabalho.

O bloqueio excessivo de banco de dados denota uma condição em que a contagem de bloqueios em um banco de dados tem limites absolutos cruzados. Essa stat indica uma degradação do desempenho da carga de trabalho. A pressão de memória é uma condição em que o número de threads solicitando concessões de memória ultrapassou um limite absoluto. Essa stat indica uma degradação do desempenho da carga de trabalho.

Outra detecção de estatísticas de espera indica uma condição em que diversas métricas medidas por meio do Repositório de Consultas estatísticas de espera cruzam um limite absoluto. Essas estatísticas indicam a degradação do desempenho da carga de trabalho.

Depois que são detectados tempos de espera excessivos, dependendo dos dados disponíveis, o log de diagnóstico de Intelligent Insights gera hashes das consultas que afetam e afetadas degradadas no desempenho, detalhes das métricas que fazem com que as consultas aguardem na execução e tempo de espera medido.

## <a name="errored-requests"></a>Solicitações com erros

O modelo de degradação de solicitações com erros monitora consultas individuais e detecta um aumento no número de consultas com erros em comparação com o período de linha de base. Esse modelo também monitora as exceções críticas que cruzam os limites absolutos gerenciados pela inteligência interna do banco de dados SQL. O sistema considera automaticamente o número de solicitações de consulta feitas ao banco de dados e as contas para qualquer alteração de carga de trabalho no período monitorado.

Quando o aumento medido em solicitações com erros relativas ao número total de solicitações feitas é considerado significativo para o desempenho da carga de trabalho, as consultas afetadas são sinalizadas como problemas de degradação do desempenho de solicitações com erros.

O log de Intelligent Insights gera a contagem de solicitações com erros. Indica se a degradação do desempenho estava relacionada a um aumento nas solicitações com erros ou ao cruzamento de um limite de exceção crítico monitorado e tempo medido da degradação do desempenho.

Se qualquer uma das exceções críticas monitoradas cruzar os limites absolutos gerenciados pelo sistema, uma percepção inteligente será gerada com detalhes de exceção crítica.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [solucionar problemas de desempenho do banco de dados SQL com o Intelligent insights](sql-database-intelligent-insights-troubleshoot-performance.md).
- Use o [log de diagnóstico de desempenho do banco de dados SQL Intelligent insights](sql-database-intelligent-insights-use-diagnostics-log.md).
- Saiba como [monitorar o banco de dados SQL usando a análise de SQL](../azure-monitor/insights/azure-sql.md).
- Saiba como [coletar e consumir dados de log de seus recursos do Azure](../azure-monitor/platform/resource-logs-overview.md).
