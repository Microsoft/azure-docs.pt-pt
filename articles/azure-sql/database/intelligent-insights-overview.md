---
title: Monitorize o desempenho da base de dados com Insights Inteligentes
description: Insights Inteligentes na Base de Dados Azure SQL e Azure SQL Managed Instance usa inteligência incorporada para monitorizar continuamente o uso da base de dados através da inteligência artificial e detetar eventos disruptivos que causam um fraco desempenho.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 06/12/2020
ms.openlocfilehash: 61033e3eb8264c1e462faac3e4553a855a1d06c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100592083"
---
# <a name="intelligent-insights-using-ai-to-monitor-and-troubleshoot-database-performance-preview"></a>Insights Inteligentes utilizando a IA para monitorizar e resolver problemas de desempenho da base de dados (pré-visualização)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Insights Inteligentes na Base de Dados Azure SQL e Azure SQL Managed Instance permite-lhe saber o que está a acontecer com o desempenho da sua base de dados.

A Intelligent Insights usa inteligência incorporada para monitorizar continuamente o uso da base de dados através da inteligência artificial e detetar eventos disruptivos que causam um fraco desempenho. Uma vez detetado, é realizada uma análise detalhada que gera um registo de recursos de Insights Inteligentes (chamado SQLInsights) com uma avaliação inteligente do problema. Esta avaliação consiste numa análise de causa-raiz da questão do desempenho da base de dados e, sempre que possível, de recomendações para melhorias de desempenho.

## <a name="what-can-intelligent-insights-do-for-you"></a>O que pode a Intelligent Insights fazer por si

Intelligent Insights é uma capacidade única da inteligência incorporada Azure que fornece o seguinte valor:

- Monitorização proativa
- Insights de desempenho personalizados
- Deteção precoce da degradação do desempenho da base de dados
- Análise de causa raiz de problemas detetados
- Recomendações de melhoria de desempenho
- Reduzir a capacidade em centenas de milhares de bases de dados
- Impacto positivo para os recursos da DevOps e o custo total de propriedade

## <a name="how-does-intelligent-insights-work"></a>Como funcionam as Ideias Inteligentes

A Intelligent Insights analisa o desempenho da base de dados comparando a carga de trabalho da base de dados da última hora com a carga de trabalho de base de sete dias passada. A carga de trabalho da base de dados é composta por consultas determinadas como as mais significativas para o desempenho da base de dados, como as consultas mais repetidas e maiores. Como cada base de dados é única com base na sua estrutura, dados, utilização e aplicação, cada linha de base de carga de trabalho gerada é específica e única a essa carga de trabalho. A Intelligent Insights, independente da linha de base da carga de trabalho, também monitoriza os limiares operacionais absolutos e deteta problemas com tempos de espera excessivos, exceções críticas e problemas com parâmetros de consulta que podem afetar o desempenho.

Após um problema de degradação do desempenho ser detetado a partir de várias métricas observadas usando inteligência artificial, a análise é realizada. Um registo de diagnóstico é gerado com uma visão inteligente do que está a acontecer com a sua base de dados. A Intelligent Insights facilita o rastreio da questão do desempenho da base de dados desde a sua primeira aparição até à resolução. Cada problema detetado é rastreado através do seu ciclo de vida desde a deteção inicial de problemas e verificação da melhoria do desempenho até à sua conclusão.

![Fluxo de trabalho de análise de desempenho da base de dados](./media/intelligent-insights-overview/intelligent-insights-concept.png)

As métricas utilizadas para medir e detetar problemas de desempenho na base de dados baseiam-se na duração da consulta, nos pedidos de tempo limite, nos tempos de espera excessivos e nos pedidos de erro. Para obter mais informações sobre métricas, consulte [as métricas de Deteção.](#detection-metrics)

As degradações de desempenho da base de dados identificadas são registadas no registo SQLInsights com entradas inteligentes que consistem nas seguintes propriedades:

| Propriedade | Detalhes |
| :------------------- | ------------------- |
| Informação da base de dados | Metadados sobre uma base de dados na qual foi detetada uma visão, como um URI de recurso. |
| Intervalo de tempo observado | Tempo de início e fim para o período da visão detetada. |
| Métricas impactadas | Métricas que causaram a visão a ser gerada: <ul><li>Aumento da duração da consulta [segundos].</li><li>Espera excessiva [segundos].</li><li>Pedidos de out-out cronometrado [percentagem].</li><li>Pedidos de saída erros [percentagem].</li></ul>|
| Valor do impacto | Valor de uma métrica medida. |
| Consultas impactadas e códigos de erro | Código de haxixe ou erro de consulta. Estes podem ser usados para se correlacionar facilmente com as consultas afetadas. São fornecidas métricas que consistem em aumento de duração da consulta, tempo de espera, contagem de tempo limite ou códigos de erro. |
| Deteções | Deteção identificada na base de dados durante o momento de um evento. Há 15 padrões de deteção. Para obter mais informações, consulte [problemas de desempenho da base de dados de resolução de problemas com Insights Inteligentes.](intelligent-insights-troubleshoot-performance.md) |
| Análise da origem do problema | A análise da causa raiz do problema identificado num formato legível pelo homem. Alguns insights podem conter uma recomendação de melhoria de desempenho sempre que possível. |
|||

Para obter uma visão geral prática sobre a utilização de Insights Inteligentes com Azure SQL Analytics e para cenários típicos de utilização, consulte este vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

Intelligent Insights brilha na descoberta e resolução de problemas problemas de desempenho da base de dados. Para utilizar insights inteligentes para resolver problemas de desempenho na base de dados, consulte [problemas de desempenho de resolução de problemas com Insights Inteligentes](intelligent-insights-troubleshoot-performance.md).

## <a name="intelligent-insights-options"></a>Opções de Insights Inteligentes

As opções de Insights Inteligentes disponíveis são:

| Opção Insights Inteligentes | Suporte à base de dados Azure SQL | Suporte de Instância Gerida do SQL do Azure |
| :----------------------------- | ----- | ----- |
| **Configure Insights Inteligentes** - Configurar análise de Insights Inteligentes para as suas bases de dados. | Yes | Yes |
| **Stream insights para Azure SQL Analytics** -- Stream insights para Azure SQL Analytics. | Yes | Yes |
| **Stream insights para Azure Event Hubs** - Stream insights para Os Centros de Eventos para mais integrações personalizadas. | Yes | Yes |
| **Stream insights para Azure Storage** - Stream insights para Azure Storage para mais análises e arquivos a longo prazo. | Yes | Yes |

> [!NOTE]
> Insights inteligentes é uma característica de pré-visualização, não disponível nas seguintes regiões: Europa Ocidental, Norte da Europa, EUA 1 e Leste dos EUA 1.

## <a name="configure-the-export-of-the-intelligent-insights-log"></a>Configure a exportação do diário de insights inteligentes

A saída dos Insights Inteligentes pode ser transmitida para um dos vários destinos para análise:

- A saída transmitida para um espaço de trabalho log Analytics pode ser usada com [a Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md) para visualizar insights através da interface de utilizador do portal Azure. Esta é a solução Azure integrada, e a forma mais típica de ver insights.
- A saída transmitida para Azure Event Hubs pode ser usada para o desenvolvimento de cenários de monitorização e alerta personalizados
- A saída transmitida para o Azure Storage pode ser usada para o desenvolvimento de aplicações personalizadas, tais como, por exemplo, relatórios personalizados, arquivo de dados de longo prazo e assim por diante.

A integração do Azure SQL Analytics, Azure Event Hubs, Azure Storage ou produtos de terceiros para consumo é realizada através da primeira ativação do registo de Insights Inteligentes (o registo "SQLInsights") na lâmina de definições de diagnóstico de uma base de dados e, em seguida, configurar dados de registo de Insights Inteligentes para serem transmitidos num desses destinos.

Para obter mais informações sobre como permitir a exploração de insights inteligentes e configurar dados métricos e de registo de recursos para serem transmitidos para um produto consumista, consulte [métricas e diagnósticos de registo](metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

### <a name="set-up-with-azure-sql-analytics"></a>Configurar com Azure SQL Analytics

A solução Azure SQL Analytics fornece interface gráfica do utilizador, reportando e alertando as capacidades no desempenho da base de dados, utilizando os dados de registo de recursos de Insights Inteligentes.

Adicione a Azure SQL Analytics ao seu painel de instrumentos do portal Azure a partir do mercado e para criar um espaço de trabalho, consulte [configurar a Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md#configuration)

Para utilizar Insights Inteligentes com Azure SQL Analytics, configurar dados de registo de insights inteligentes para serem transmitidos para o espaço de trabalho Azure SQL Analytics que criou no passo anterior, ver [Métricas e registo de diagnósticos.](metrics-diagnostic-telemetry-logging-streaming-export-configure.md)

O exemplo a seguir mostra um Intelligent Insights visualizado através do Azure SQL Analytics:

![Relatório de Insights Inteligentes](./media/intelligent-insights-overview/intelligent-insights-azure-sql-analytics.png)

### <a name="set-up-with-event-hubs"></a>Configurar com centros de eventos

Para utilizar Insights Inteligentes com Centros de Eventos, configurar dados de registo de insights inteligentes para serem transmitidos para Centros de Eventos, ver [métricas e diagnósticos de registo](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) e [stream Azure registos de diagnóstico para Centros de Eventos](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs).

Para utilizar os Centros de Eventos para configurar monitorização e alerta personalizados, consulte [o que fazer com métricas e registos de diagnóstico em Centros de Eventos](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#what-to-do-with-metrics-and-resource-logs-in-event-hubs).

### <a name="set-up-with-azure-storage"></a>Configurar com armazenamento Azure

Para utilizar insights inteligentes com armazenamento, configurar dados de registo de insights inteligentes para serem transmitidos para armazenamento, ver [métricas e diagnósticos registando](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) e [stream para O Armazenamento Azure](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#stream-into-azure-storage).

### <a name="custom-integrations-of-intelligent-insights-log"></a>Integrações personalizadas do registo de Insights Inteligentes

Para utilizar Insights Inteligentes com ferramentas de terceiros, ou para o desenvolvimento de alerta e monitorização personalizado, consulte [o registo de diagnóstico de desempenho da base de dados de Insights Inteligentes](intelligent-insights-use-diagnostics-log.md).

## <a name="detection-metrics"></a>Métricas de deteção

As métricas utilizadas para modelos de deteção que geram Insights Inteligentes baseiam-se na monitorização:

- Duração da consulta
- Pedidos de tempo limite
- Tempo de espera excessivo
- Errou os pedidos

Os pedidos de duração e timeout são usados como modelos primários na deteção de problemas com o desempenho da carga de trabalho da base de dados. São usados porque medem diretamente o que está a acontecer com a carga de trabalho. Para detetar todos os casos possíveis de degradação do desempenho da carga de trabalho, o tempo de espera excessivo e os pedidos de desativação são utilizados como modelos adicionais para indicar problemas que afetam o desempenho da carga de trabalho.

O sistema considera automaticamente alterações na carga de trabalho e alterações no número de pedidos de consulta feitos na base de dados para determinar dinamicamente os limiares normais e fora do comum de desempenho da base de dados.

Todas as métricas são consideradas juntas em várias relações através de um modelo de dados cientificamente derivado que categoriza cada problema de desempenho detetado. As informações fornecidas através de uma informação inteligente incluem:

- Detalhes da questão de desempenho detetados.
- Uma análise de causa-raiz do problema detetado.
- Recomendações sobre como melhorar o desempenho da base de dados monitorizada, sempre que possível.

## <a name="query-duration"></a>Duração da consulta

O modelo de degradação da duração da consulta analisa as consultas individuais e deteta o aumento do tempo necessário para compilar e executar uma consulta em comparação com a linha de base de desempenho.

Se a inteligência incorporada detetar um aumento significativo no tempo de compilação de consultas ou de execução de consultas que afete o desempenho da carga de trabalho, estas consultas são sinalizadas como problemas de degradação do desempenho da duração da consulta.

O registo de diagnósticos intelligent insights coloca em causa a consulta do haxixe da consulta degradada no desempenho. O haxixe de consulta indica se a degradação do desempenho estava relacionada com a compilação de consultas ou o aumento do tempo de execução, o que aumentou o tempo de duração da consulta.

## <a name="timeout-requests"></a>Pedidos de tempo limite

O timeout solicita a degradação do modelo analisa consultas individuais e deteta qualquer aumento de intervalos no nível de execução de consultas e os intervalos globais de pedidos ao nível da base de dados em comparação com o período de base de desempenho.

Algumas das consultas podem sair mesmo antes de chegarem à fase de execução. Através dos meios de trabalhadores abortados vs. pedidos feitos, medidas de inteligência incorporadas e analisa todas as consultas que chegaram à base de dados se chegaram ou não à fase de execução.

Após o número de intervalos para consultas executadas ou o número de trabalhadores de pedido abortado atravessar o limiar gerido pelo sistema, um registo de diagnóstico é preenchido com insights inteligentes.

Os insights gerados contêm o número de pedidos de tempo e o número de consultas cronometradas. A indicação da degradação do desempenho está relacionada com o aumento do timeout na fase de execução, ou o nível global da base de dados é fornecido. Quando o aumento dos intervalos é considerado significativo para o desempenho da base de dados, estas consultas são sinalizadas como problemas de degradação do desempenho no intervalo.

## <a name="excessive-wait-times"></a>Tempos de espera excessivos

O modelo de tempo de espera excessivo monitoriza consultas individuais de base de dados. Deteta estatísticas de espera de consultas invulgarmente elevadas que cruzaram os limiares absolutos geridos pelo sistema. Observam-se as seguintes métricas de tempo de espera excessivas utilizando, estatísticas de [espera da loja de consultas (sys.query_store_wait_stats)](/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql):

- Atingir limites de recursos
- Atingindo limites de recursos elásticos da piscina
- Número excessivo de fios de trabalhador ou de sessão
- Bloqueio excessivo da base de dados
- Pressão da memória
- Outras estatísticas de espera

Atingindo limites de recursos ou limites de recursos elásticos de conjunto denotam que o consumo de recursos disponíveis numa subscrição ou na piscina elástica cruzou limiares absolutos. Estas estatísticas indicam a degradação do desempenho da carga de trabalho. Um número excessivo de fios de trabalho ou de sessão denota uma condição em que o número de roscas ou sessões iniciadas pelo trabalhador ultrapassou os limiares absolutos. Estas estatísticas indicam a degradação do desempenho da carga de trabalho.

O bloqueio excessivo da base de dados denota uma condição em que a contagem de fechaduras numa base de dados ultrapassou os limiares absolutos. Esta estatística indica uma degradação do desempenho da carga de trabalho. A pressão da memória é uma condição em que o número de fios que solicitam subsídios de memória ultrapassou um limiar absoluto. Esta estatística indica uma degradação do desempenho da carga de trabalho.

Outras estatísticas de espera indicam uma condição em que métricas diversas medida através das Estatísticas de Espera da Loja de Consultas ultrapassaram um limiar absoluto. Estas estatísticas indicam a degradação do desempenho da carga de trabalho.

Após serem detetados tempos de espera excessivos, dependendo dos dados disponíveis, os Insights Inteligentes diagnosticam as saídas das consultas afetos e afetadas degradadas no desempenho, detalhes das métricas que fazem com que as consultas aguardem a execução e mediu o tempo de espera.

## <a name="errored-requests"></a>Pedidos erros

O modelo de degradação de pedidos errados monitoriza as consultas individuais e deteta um aumento no número de consultas que errou em comparação com o período de base. Este modelo também monitoriza exceções críticas que ultrapassaram limiares absolutos geridos pela inteligência incorporada. O sistema considera automaticamente o número de pedidos de consulta feitos à base de dados e contabiliza quaisquer alterações de carga de trabalho no período monitorizado.

Quando o aumento medido de pedidos de erro em relação ao número total de pedidos apresentados é considerado significativo para o desempenho da carga de trabalho, as consultas afetadas são sinalizadas como pedidos errados problemas de degradação do desempenho.

O Registo de Insights Inteligentes regista a contagem de pedidos erros. Indica se a degradação do desempenho estava relacionada com um aumento dos pedidos por erro ou com a passagem de um limiar de exceção crítica monitorizado e o tempo medido da degradação do desempenho.

Se alguma das exceções críticas monitorizadas atravessar os limiares absolutos geridos pelo sistema, uma visão inteligente é gerada com detalhes críticos de exceção.

## <a name="next-steps"></a>Passos seguintes

- Saiba como monitorizar as [bases de dados utilizando o SQL Analytics](../../azure-monitor/insights/azure-sql.md).
- Saiba como [resolver problemas de desempenho com Insights Inteligentes.](intelligent-insights-troubleshoot-performance.md)