---
title: Monitorizar o desempenho da base de dados com informações inteligentes - base de dados SQL do Azure | Documentos da Microsoft
description: Azure informações inteligentes de base de dados SQL utiliza inteligência incorporada para monitorizar a utilização da base de dados através de inteligência artificial e detetar eventos disruptivos que provocar um mau desempenho continuamente.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: 15154844c954e53ca1add5d3fbaa3e9d02152ad2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60703216"
---
# <a name="intelligent-insights-using-ai-to-monitor-and-troubleshoot-database-performance"></a>Informações inteligentes com ia para monitorizar e resolver problemas de desempenho de base de dados

Azure informações inteligentes de base de dados SQL permite-lhe saber o que acontece com o desempenho de base de dados SQL e a instância gerida da base de dados.

Informações inteligentes utiliza inteligência incorporada para monitorizar a utilização da base de dados através de inteligência artificial e detetar eventos disruptivos que provocar um mau desempenho continuamente. Depois de detetada, é executada uma análise detalhada que gera um registo de diagnóstico com uma avaliação inteligente do problema. Essa avaliação é composta por uma análise da causa do problema de desempenho da base de dados e, sempre que possível, recomendações de melhorias de desempenho.

## <a name="what-can-intelligent-insights-do-for-you"></a>O que pode informações inteligentes fazer por

Informações inteligentes é um recurso exclusivo de inteligência incorporada do Azure que fornece o seguinte valor:

- Monitorização proativa
- Informações de desempenho sob medida
- Detecção antecipada de degradação do desempenho da base de dados
- Análise de problemas detetados de causa raiz
- Recomendações de melhorias de desempenho
- Aumentar horizontalmente a capacidade em centenas de milhares de bases de dados
- Impacto positivo para recursos de DevOps e o custo total de propriedade

## <a name="how-does-intelligent-insights-work"></a>Como funciona o Intelligent Insights

Informações inteligentes analisa o desempenho da base de dados ao comparar a carga de trabalho da base de dados de última hora com a carga de trabalho de linha de base de sete dias anteriores. Carga de trabalho de base de dados é composta por consultas identificadas como sendo o mais significativo para o desempenho de base de dados, tais como as consultas de maiores e mais repetidas. Uma vez que cada base de dados é exclusivo com base na sua estrutura, a dados, a utilização e a aplicação, cada linha de base de carga de trabalho que é gerada é específico e exclusivo para uma instância individual. Informações inteligentes, independentemente da linha de base de carga de trabalho, também monitora os limiares de operacionais absolutos e Deteta os problemas com tempos de espera excessivos, exceções críticas e problemas com parameterizations de consulta que podem afetar o desempenho.

Depois de um problema de degradação do desempenho é detectado a partir de várias métricas observadas, ao utilizar a inteligência artificial, análise é executada. Um registo de diagnóstico é gerado com informações inteligentes sobre o que acontece com a base de dados. Informações inteligentes torna mais fácil controlar o problema de desempenho da base de dados da sua aparência primeiro até à resolução. Cada detetou o problema é controlado por meio do seu ciclo de vida de deteção inicial do problema e a verificação de melhoria de desempenho para a sua conclusão.

![Fluxo de trabalho de análise de desempenho de base de dados](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)

As métricas utilizadas para medir e detetar problemas de desempenho de base de dados baseiam-se na duração de consulta, tempo limite de pedidos, tempos de espera excessivos e pedidos com erros. Para obter mais informações sobre as métricas, consulte a [métricas de deteção](sql-database-intelligent-insights.md#detection-metrics) seção deste documento.

Identificado a base de dados SQL degradações de desempenho são registradas no log de diagnóstico com entradas inteligentes que consistem as seguintes propriedades:

| Propriedade             | Detalhes              |
| :------------------- | ------------------- |
| informações da base de dados | Metadados sobre uma base de dados em que foi detetada uma informação, como um URI do recurso. |
| Intervalo de tempo observado | Hora de início e fim para o período da informação detetado. |
| Métricas de afetados | Métricas que causou uma informação a ser gerado: <ul><li>Consulta a aumentar a duração [segundos].</li><li>Espera excessiva [segundos].</li><li>Atingiu o limite de pedidos [percentagem].</li><li>Pedidos de escalamento com erros [percentagem].</li></ul>|
| Valor de impacto | Valor de uma métrica medido. |
| As consultas afetadas e códigos de erro | Código de erro ou de hash de consulta. Estes podem ser utilizados para correlacionar facilmente para consultas afetadas. As métricas que consistem de aumento de duração de consulta, tempo de espera, contagens de tempo limite ou códigos de erro são fornecidas. |
| Deteções | Deteção identificada na base de dados durante o tempo de um evento. Há 15 padrões de deteção. Para obter mais informações, consulte [resolver problemas de desempenho de base de dados com informações inteligentes](sql-database-intelligent-insights-troubleshoot-performance.md). |
| Análise da origem do problema | Analisar o problema identificado num formato legível por humanos a causa raiz. Algumas informações podem conter uma recomendação de melhoria do desempenho, sempre que possível. |
|||

Para obter uma descrição geral prática sobre a utilização de informações inteligentes com a análise de SQL do Azure e para cenários de uso típico, veja o vídeo incorporado:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

Informações inteligentes se destaca em descobrir e resolução de problemas de desempenho da base de dados SQL. Para utilizar informações inteligentes para resolver problemas de desempenho de base de dados de base de dados SQL e a instância gerida, veja [problemas de desempenho de resolver problemas relacionados com a base de dados de SQL do Azure com informações inteligentes](sql-database-intelligent-insights-troubleshoot-performance.md).

## <a name="configure-intelligent-insights"></a>Configurar informações inteligentes

O resultado das informações inteligentes é um registo de diagnóstico de desempenho inteligente. Este registo pode ser consumido de várias formas - por meio de transmissão em fluxo em-la para análise de SQL do Azure, os Hubs de eventos do Azure e o armazenamento do Azure, ou uma aplicação de produto de terceiros.

- Utilizar o produto com [a análise de SQL do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) para ver informações através da interface de utilizador do portal do Azure. Esta é a solução do Azure integrada e a forma mais comum para ver informações.
- Usar o produto com os Hubs de eventos do Azure para o desenvolvimento de cenários de alertas e monitorização personalizada
- Utilizar o produto com o armazenamento do Azure para desenvolvimento de aplicativos personalizados, como por exemplo relatório personalizado, arquivo de dados de longo prazo e assim por diante.

Integração de informações inteligentes com outros produtos de análise de SQL do Azure, Hub de eventos do Azure, o armazenamento do Azure ou produtos de terceiros para consumo é realizada por meio do primeiro ativar informações inteligentes de registo (o registo de "SQLInsights") no diagnóstico Painel de definições de uma base de dados e, em seguida, configurar informações inteligentes registos de dados serão transmitidos para um dos dois.

Para obter mais informações sobre como ativar o registo de informações inteligentes e configurar dados de registo para ser transmitido a um produto de consumo, consulte [métricas de base de dados do Azure SQL e o registo de diagnósticos](sql-database-metrics-diag-logging.md).

### <a name="set-up-with-azure-sql-analytics"></a>Configurar a análise de SQL do Azure

Solução de análise de SQL do Azure fornece a interface gráfica do usuário, relatórios e alertas de recursos sobre o desempenho da base de dados, juntamente com o Intelligent Insights dados de registo de diagnóstico.

> [!TIP]
> Guia de introdução: É a maneira mais fácil de obter deslocar o com a utilização de informações inteligentes para utilizá-lo juntamente com a análise de SQL do Azure que irá fornecer uma interface gráfica do usuário para problemas de desempenho da base de dados. Adicionar solução de análise de SQL do Azure a partir do marketplace, criar uma área de trabalho dentro desta solução e, em seguida, para cada base de dados que pretende ativar informações inteligentes em configurar a transmissão em fluxo do registo de "SQLInsights" no painel de definições de diagnóstico de uma base de dados para o área de trabalho da análise de SQL do Azure.
>

Pré-requisito é ter adicionado ao dashboard do portal do Azure do marketplace a análise de SQL do Azure e para criar uma área de trabalho, consulte [configure a análise de SQL do Azure](../azure-monitor/insights/azure-sql.md#configuration)

Para utilizar informações inteligentes com a análise de SQL do Azure, configurar dados de registo de informações inteligentes para ser transmitido para a área de trabalho de análise de SQL do Azure que criou no passo anterior, consulte [métricas de base de dados do Azure SQL e o diagnóstico de registo](sql-database-metrics-diag-logging.md).

O exemplo seguinte mostra que um informações inteligentes exibida por meio de análise de SQL do Azure:

![Relatório de informações inteligente](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

### <a name="set-up-with-event-hubs"></a>Configurar com os Hubs de eventos

Para utilizar informações inteligentes com os Hubs de eventos, configurar dados de registo de informações inteligentes para ser transmitido para os Hubs de eventos, consulte [registos de diagnóstico do Stream Azure para os Hubs de eventos](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md).

Para utilizar os Hubs de eventos para configurar a monitorização personalizada e os alertas, consulte o artigo [o que fazer com métricas e diagnósticos registos nos Hubs de eventos](sql-database-metrics-diag-logging.md#what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs).

### <a name="set-up-with-azure-storage"></a>Configurar o armazenamento do Azure

Para utilizar informações inteligentes com o armazenamento, configurar dados de registo de informações inteligentes para ser transmitido para o armazenamento, consulte [Stream para o armazenamento do Azure](sql-database-metrics-diag-logging.md#stream-into-storage).

### <a name="custom-integrations-of-intelligent-insights-log"></a>Integrações personalizadas de log de informações inteligentes

Para utilizar informações inteligentes com ferramentas de terceiros, ou para alertas personalizados e monitorização de desenvolvimento, consulte [uso de registo de diagnóstico de desempenho de base de dados de informações inteligentes](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="detection-metrics"></a>Métricas de deteção

Métricas utilizadas para modelos de deteção que geram informações inteligentes baseiam-se sobre como monitorizar:

- Duração da consulta
- Pedidos de tempo limite
- Tempo de espera excessivos
- Terminou devido a erros pedidos

Pedidos de duração e o tempo limite da consulta são usados como modelos principais na detecção de problemas com o desempenho da carga de trabalho de base de dados. Eles são usados porque eles medem diretamente o que acontece com a carga de trabalho. Para detetar todos os casos possíveis de desempenho da carga de trabalho degradação, excesso de tempo de espera e pedidos com erros de saída são usados como modelos adicionais para indicar problemas que afetam o desempenho da carga de trabalho.

O sistema considera automaticamente as alterações à carga de trabalho e as alterações no número de pedidos de consulta efetuadas dinamicamente a base de dados para determinam os limiares de desempenho de base de dados normais e de fora do comum.

Todas as métricas são consideradas em conjunto em várias relações através de um modelo de dados derivados scientifically que categoriza cada problema de desempenho detetado. Incluem informações fornecidas por meio de uma informação inteligente:

- Detalhes do problema de desempenho detetados.
- Uma análise da causa do problema detetado.
- Recomendações sobre como melhorar o desempenho da base de dados monitorizado do SQL, sempre que possível.

## <a name="query-duration"></a>Duração da consulta

O modelo de degradação de duração de consulta analisa consultas individuais e Deteta o aumento do tempo necessário para compilar e executar uma consulta em comparação comparada a linha de base de desempenho.

Se a inteligência incorporada da base de dados SQL Deteta um aumento significativo na compilação de consulta ou tempo de execução de consulta que afeta o desempenho da carga de trabalho, estas consultas são sinalizadas como duração de consulta problemas de degradação do desempenho.

O registo de diagnóstico de informações inteligentes produz o hash de consulta da consulta degradada no desempenho. O hash de consulta indica se a degradação do desempenho estava relacionada a consulta compilação ou tempo de execução aumento, que aumenta o tempo de duração de consulta.

## <a name="timeout-requests"></a>Pedidos de tempo limite

O modelo de degradação de pedidos de tempo limite analisa consultas individuais e Deteta um aumento em tempos limite no nível de execução da consulta e os tempos limite de pedido geral ao nível da base de dados em comparação comparado o período de linha de base de desempenho.

Tempo limite algumas das consultas podem até mesmo antes de atingirem o estágio de execução. Por meio dos meios de trabalhadores abortados vs. os pedidos efetuados, inteligência incorporada da base de dados SQL avalia e analisa todas as consultas que chegaram a base de dados se eles poderiam o estágio de execução ou não.

Depois do número de tempos limite para consultas executadas ou o número de trabalhadores de pedido abortadas ultrapassar o limiar gerenciados pelo sistema, um registo de diagnóstico é preenchido com informações inteligentes.

As informações geradas contêm o número de pedidos excedeu o tempo e o número de consultas atingiu o limite. Indicação de que a degradação do desempenho está relacionado com o aumento do tempo limite na fase de execução ou o nível de base de dados geral é fornecido. Quando o aumento em tempos limite é considerado significativo para desempenho da base de dados, estas consultas são sinalizadas como problemas de degradação do desempenho de tempo limite.

## <a name="excessive-wait-times"></a>Tempos de espera excessivos

O modelo de tempo de espera excessivos monitoriza as consultas de base de dados individuais. Deteta as estatísticas de espera do anormalmente elevado de consulta que ultrapassou os limiares absolutos gerenciados pelo sistema. Métricas seguintes tempo de espera excessivos de consulta são respeitadas utilizando o novo recurso do SQL Server, consulta Store aguardar estatísticas (sys.query_store_wait_stats):

- Alcance limites de recursos
- Alcance limites de recursos do conjunto elástico
- Número excessivo de threads de trabalho ou de sessão
- Bloqueio excessivo da base de dados
- Pressão de memória
- Outras estatísticas de espera

Alcance limites de recursos ou limites de recursos do conjunto elástico denotam que o consumo de recursos disponíveis numa assinatura ou do conjunto elástico ultrapassados limiares absolutos. Estas estatísticas indicam degradação do desempenho de carga de trabalho. Um número excessivo de threads de trabalho ou de sessão indica uma condição em que o número de threads de trabalho ou sessões iniciadas ultrapassados limiares absolutos. Estas estatísticas indicam degradação do desempenho de carga de trabalho.

Bloqueio excessivo da base de dados indica uma condição em que a contagem de bloqueios num banco de dados ultrapassou limiares absolutos. Este stat indica uma degradação do desempenho de carga de trabalho. Memória pressão é uma condição em que o número de threads solicitando memória concede ultrapassou um limiar absoluto. Este stat indica uma degradação do desempenho de carga de trabalho.

Outra deteção de estatísticas de espera indica uma condição em que diversas métricas medidas através das estatísticas de espera da consulta Store ultrapassou um limiar de absoluto. Estas estatísticas indicam degradação do desempenho de carga de trabalho.

Depois de tempos de espera excessivos são detectados, consoante os dados disponíveis, o diagnóstico de informações inteligentes iniciar hashes de saídas das consultas afetados e que afetam uma degradação no desempenho, detalhes das métricas que fazem com que consultas a aguardar na execução, e tempo de espera de medida.

## <a name="errored-requests"></a>Pedidos com erros

O modelo de degradação pedidos com erros, monitores indivíduo consulta e Deteta um aumento no número de consultas que terminou devido a erros em comparação com o período de linha de base. Este modelo também monitora exceções críticas que ultrapassados limiares absolutos geridos pelo inteligência incorporada da base de dados SQL. O sistema considera automaticamente o número de pedidos de consulta feita para a base de dados e contas para efetuar quaisquer alterações de carga de trabalho no período de monitorizados.

Quando o aumento medido nos pedidos com erros relativamente ao número de pedidos efetuados geral é considerado significativo para desempenho da carga de trabalho, as consultas afetadas são sinalizadas como problemas de degradação do desempenho de pedidos com erros.

O registo de informações inteligentes produz a contagem de pedidos com erros. Indica se a degradação do desempenho estava relacionada para um aumento de pedidos com erros ou para cruzar um limiar de exceção crítica monitorizado e a hora de medida da degradação do desempenho.

Se qualquer uma das exceções críticas monitorizadas cruzar os limiares absolutos gerenciados pelo sistema, é gerada uma informação inteligente com os detalhes da exceção crítica.

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [resolver problemas de desempenho de base de dados SQL com informações inteligentes](sql-database-intelligent-insights-troubleshoot-performance.md).
- Utilize o [registo de diagnóstico de desempenho de base de dados de SQL Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).
- Saiba como [monitorizar base de dados SQL com a análise de SQL](../azure-monitor/insights/azure-sql.md).
- Saiba como [recolher e consumir dados de registo dos seus recursos do Azure](../azure-monitor/platform/diagnostic-logs-overview.md).
