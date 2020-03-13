---
title: Monitorizar o desempenho da base de dados com Insights Inteligentes
description: A Base de Dados Azure SQL Intelligent Insights utiliza inteligência incorporada para monitorizar continuamente o uso da base de dados através da inteligência artificial e detetar eventos disruptivos que causam um fraco desempenho.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: d7b9ada17871dc7882209b7a8a449a8edcd61a94
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79214080"
---
# <a name="intelligent-insights-using-ai-to-monitor-and-troubleshoot-database-performance-preview"></a>Insights Inteligentes que usam IA para monitorizar e resolver o desempenho da base de dados (Pré-visualização)

A Base de Dados Azure SQL Intelligent Insights informa-o sobre o que se passa com o desempenho da sua base de dados.

A Intelligent Insights usa inteligência incorporada para monitorizar continuamente o uso da base de dados através da inteligência artificial e detetar eventos disruptivos que causam um fraco desempenho. Uma vez detetado, é realizada uma análise detalhada que gera um registo de recursos Smart Insights (chamado SQLInsights) com uma avaliação inteligente do problema. Esta avaliação consiste numa análise de causas fundamentais do problema de desempenho da base de dados e, sempre que possível, recomendações para melhorias de desempenho.

## <a name="what-can-intelligent-insights-do-for-you"></a>O que os Insights Inteligentes podem fazer por si

Smart Insights é uma capacidade única da inteligência incorporada do Azure que fornece o seguinte valor:

- Monitorização proativa
- Insights de desempenho personalizados
- Deteção precoce da degradação do desempenho da base de dados
- Análise de causa seleções de problemas detetados
- Recomendações de melhoria do desempenho
- Aumentar a capacidade em centenas de milhares de bases de dados
- Impacto positivo nos recursos da DevOps e o custo total da propriedade

## <a name="how-does-intelligent-insights-work"></a>Como funciona a Intelligent Insights

A Intelligent Insights analisa o desempenho da base de dados comparando a carga de trabalho da base de dados da última hora com a carga de trabalho de base de sete dias. A carga de trabalho na base de dados é composta por consultas determinadas como as mais significativas para o desempenho da base de dados, como as consultas mais repetidas e maiores. Como cada base de dados é única com base na sua estrutura, dados, utilização e aplicação, cada linha de base de carga de trabalho gerada é específica e única para essa carga de trabalho. A Intelligent Insights, independente da linha de base da carga de trabalho, também monitoriza limiares operacionais absolutos e deteta problemas com tempos de espera excessivos, exceções críticas e problemas com parametrismos de consulta que podem afetar o desempenho.

Após um problema de degradação do desempenho ser detetado a partir de múltiplas métricas observadas usando inteligência artificial, a análise é realizada. Um registo de diagnóstico é gerado com uma visão inteligente do que está a acontecer com a sua base de dados. A Intelligent Insights facilita a compor o problema de desempenho da base de dados desde a sua primeira aparição até à resolução. Cada problema detetado é rastreado através do seu ciclo de vida desde a deteção inicial de problemas e verificação da melhoria do desempenho até à sua conclusão.

![Fluxo de trabalho de análise de desempenho da base de dados](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)

As métricas utilizadas para medir e detetar problemas de desempenho na base de dados baseiam-se na duração da consulta, pedidos de tempo limite, tempos de espera excessivos e pedidos errados. Para obter mais informações sobre métricas, consulte métricas de [deteção](#detection-metrics).

As degradações de desempenho identificadas da Base de Dados SQL são registadas no registo SQLInsights com entradas inteligentes que consistem nas seguintes propriedades:

| Propriedade | Detalhes |
| :------------------- | ------------------- |
| informação sobre bases de dados | Metadados sobre uma base de dados sobre a qual foi detetada uma visão, como um uri de recursos. |
| Intervalo de tempo observado | Início e fim do tempo para o período da perceção detetada. |
| Métricas impactadas | Métricas que causaram uma perspicácia para ser gerada: <ul><li>A duração da consulta aumenta [segundos].</li><li>Espera excessiva [segundos].</li><li>Pedidos cronometrados [percentagem].</li><li>Pedidos de saída errados [percentagem].</li></ul>|
| Valor de impacto | Valor de uma métrica medida. |
| Consultas e códigos de erro impactados | Código de erro ou hash de consulta. Estes podem ser usados para facilmente correlacionar com consultas afetadas. São fornecidas métricas que consistem em aumento de duração da consulta, tempo de espera, contagem de tempo de tempo ou códigos de erro. |
| Deteções | Deteção identificada na base de dados durante o momento de um evento. Há 15 padrões de deteção. Para mais informações, consulte problemas de desempenho na base de [dados de Troubleshoot com Insights Inteligentes](sql-database-intelligent-insights-troubleshoot-performance.md). |
| Análise da origem do problema | Análise de causa seletiva do problema identificado num formato legível pelo homem. Alguns insights podem conter uma recomendação de melhoria de desempenho sempre que possível. |
|||

Para uma visão geral prática sobre a utilização de Insights Inteligentes com Ana Azure SQL e para cenários de utilização típicos, consulte este vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

A Intelligent Insights brilha na descoberta e resolução de problemas de problemas de desempenho da Base de Dados SQL. Para utilizar insights inteligentes para resolver problemas de desempenho na base de dados, consulte problemas de desempenho da [Troubleshoot Azure SQL Database com Insights Inteligentes](sql-database-intelligent-insights-troubleshoot-performance.md).

## <a name="intelligent-insights-options"></a>Opções de Insights Inteligentes

As opções de Insights Inteligentes disponíveis na Base de Dados Azure SQL são:

| Opção Insights Inteligentes | Base de dados única e suporte de base de dados agréis | Suporte de base de dados de instâncias |
| :----------------------------- | ----- | ----- |
| **Configure Insights Inteligentes** - Configure análise de Insights Inteligentes para as suas bases de dados. | Sim | Sim |
| **Stream insights para Azure SQL Analytics** -- Stream insights para Azure SQL Analytics solução de monitorização para Base de Dados Azure SQL. | Sim | Sim |
| **Stream insights para Event Hub** - Stream insights para Event Hubs para mais integrações personalizadas. | Sim | Sim |
| **Stream insights para Azure Storage** - Stream insights para Azure Storage para mais análise e arquivo de longo prazo. | Sim | Sim |

## <a name="configure-the-export-of-the-intelligent-insights-log"></a>Configure a exportação do registo Smart Insights

A saída dos Insights Inteligentes pode ser transmitida para um dos vários destinos para análise:

- A saída transmitida para um espaço de trabalho log Analytics pode ser usada com [o Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) para visualizar insights através da interface de utilizador do portal Azure. Esta é a solução Azure integrada, e a forma mais típica de ver insights.
- A saída transmitida para os Hubs de Eventos Azure pode ser usada para o desenvolvimento de cenários de monitorização e alerta personalizados
- A saída transmitida para o Armazenamento Azure pode ser usada para o desenvolvimento de aplicações personalizadas, tais como relatórios personalizados, arquivo de dados de longo prazo e assim por diante.

Integração de Azure SQL Analytics, Azure Event Hub, Armazenamento Azure ou produtos de terceiros para consumo é realizado através de primeiro permitir a exploração de Insights Inteligentes (o log "SQLInsights") na lâmina de definições de diagnóstico de uma base de dados, e depois configurar os dados de registo de Insights Inteligentes para serem transmitidos para um destes destinos.

Para obter mais informações sobre como permitir a exploração de Insights Inteligentes e configurar dados de registo de métricas e recursos para ser transmitido para um produto consumista, consulte as métricas da Base de [Dados Azure SQL e o registo de diagnósticos.](sql-database-metrics-diag-logging.md)

### <a name="set-up-with-azure-sql-analytics"></a>Configurar com Azure SQL Analytics

A solução Azure SQL Analytics fornece interface gráfica do utilizador, reportando e alertando capacidades no desempenho da base de dados, utilizando os dados de registo de recursos Intelligent Insights.

Adicione o Azure SQL Analytics ao seu portal Azure dashboard a partir do mercado e para criar um espaço de trabalho, consulte [configure Azure SQL Analytics](../azure-monitor/insights/azure-sql.md#configuration)

Para utilizar Insights Inteligentes com Ana Azure SQL Analytics, configure os dados de registo de Insights Inteligentes a serem transmitidos para o espaço de trabalho azure SQL Analytics que criou no passo anterior, consulte as métricas da Base de [Dados Azure SQL e o registo de diagnósticos.](sql-database-metrics-diag-logging.md)

O exemplo seguinte mostra um Smart Insights visualizado através do Azure SQL Analytics:

![Relatório de informações inteligente](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

### <a name="set-up-with-event-hubs"></a>Configurar com Hubs de Eventos

Para utilizar Insights Inteligentes com Hubs de Eventos, configure os dados de registo de Insights Inteligentes a serem transmitidos para Centros de Eventos, consulte as métricas de base de [dados Azure SQL e](sql-database-metrics-diag-logging.md) a exploração de diagnósticos e e os registos de diagnóstico sinuosos do Stream [Azure para Os Centros de Eventos](../azure-monitor/platform/resource-logs-stream-event-hubs.md).

Para utilizar os Hubs de Eventos para configurar monitorização e alerta personalizados, veja [o que fazer com métricas e registos](sql-database-metrics-diag-logging.md#what-to-do-with-metrics-and-resource-logs-in-event-hubs)de diagnóstico em Centros de Eventos .

### <a name="set-up-with-azure-storage"></a>Configurar com armazenamento Azure

Para utilizar Insights Inteligentes com Armazenamento, configure dados de registo de Insights Inteligentes a serem transmitidos para armazenamento, consulte as métricas da Base de [Dados Azure SQL e diagnósticos de registo](sql-database-metrics-diag-logging.md) e streaming para o armazenamento do [Azure](sql-database-metrics-diag-logging.md#stream-into-azure-storage).

### <a name="custom-integrations-of-intelligent-insights-log"></a>Integrações personalizadas do registo Smart Insights

Para utilizar insights inteligentes com ferramentas de terceiros, ou para alerta personalizado e monitorização do desenvolvimento, consulte Use o registo de [diagnósticos](sql-database-intelligent-insights-use-diagnostics-log.md)de desempenho da base de dados Intelligent Insights .

## <a name="detection-metrics"></a>Métricas de deteção

As métricas utilizadas para modelos de deteção que geram Insights Inteligentes baseiam-se na monitorização:

- Duração da consulta
- Pedidos de timeout
- Tempo de espera excessivo
- Pedidos errados

Os pedidos de duração e tempo regulamentar são utilizados como modelos primários na deteção de problemas com o desempenho da carga de trabalho na base de dados. São usados porque medem diretamente o que está a acontecer com a carga de trabalho. Para detetar todos os casos possíveis de degradação do desempenho da carga de trabalho, o tempo excessivo de espera e os pedidos de erro são usados como modelos adicionais para indicar problemas que afetam o desempenho da carga de trabalho.

O sistema considera automaticamente alterações na carga de trabalho e alterações no número de pedidos de consulta feitos na base de dados para determinar dinamicamente os limiares normais e fora do normal de desempenho da base de dados.

Todas as métricas são consideradas em conjunto em várias relações através de um modelo de dados cientificamente derivado que categoriza cada problema de desempenho detetado. A informação fornecida através de uma visão inteligente inclui:

- Detalhes do problema de desempenho detetados.
- Uma análise de causa-raiz do problema detetado.
- Recomendações sobre como melhorar o desempenho da base de dados SQL monitorizada, sempre que possível.

## <a name="query-duration"></a>Duração da consulta

O modelo de degradação da duração da consulta analisa consultas individuais e deteta o aumento do tempo necessário para compilar e executar uma consulta em comparação com a linha de base de desempenho.

Se a inteligência incorporada da Base de Dados SQL detetar um aumento significativo na compilação de consultas ou no tempo de execução da consulta que afeta o desempenho da carga de trabalho, estas consultas são sinalizadas como problemas de degradação do desempenho da duração da consulta.

O registo de diagnóstico sintetizadores Intelligent Insights produz o hash de consulta da consulta degradado no desempenho. O hash de consulta indica se a degradação do desempenho estava relacionada com a compilação de consultas ou o aumento do tempo de execução, o que aumentou o tempo de duração da consulta.

## <a name="timeout-requests"></a>Pedidos de timeout

O timeout solicita que o modelo de degradação analise as consultas individuais e detete qualquer aumento de tempos ao nível da execução da consulta e os prazos globais de pedido ao nível da base de dados em comparação com o período de linha de base de desempenho.

Algumas das consultas podem esgotar-se mesmo antes de chegarem à fase de execução. Através dos meios de trabalhadores abortados vs. pedidos feitos, a Base de Dados SQL medidas de inteligência incorporadas e analisa todas as consultas que chegaram à base de dados se chegaram à fase de execução ou não.

Após o número de intervalos para consultas executadas ou o número de trabalhadores abortados que cruzam o limiar gerido pelo sistema, um registo de diagnóstico é preenchido com insights inteligentes.

Os insights gerados contêm o número de pedidos cronometrados e o número de consultas cronometradas. A indicação da degradação do desempenho está relacionada com o aumento do tempo na fase de execução, ou o nível total de base de dados é fornecido. Quando o aumento dos prazos é considerado significativo para o desempenho da base de dados, estas consultas são sinalizadas como problemas de degradação do desempenho.

## <a name="excessive-wait-times"></a>Tempos de espera excessivos

O modelo de tempo de espera excessivo monitoriza as consultas individuais de base de dados. Deteta estatísticas de espera invulgarmente altas que atravessaram os limiares absolutos geridos pelo sistema. As seguintes métricas de tempo de espera excessivas são observadas utilizando a nova funcionalidade Do Servidor SQL, Query Store Wait Stats (sys.query_store_wait_stats):

- Atingir limites de recursos
- Atingir limites de recursos de piscina elástica
- Número excessivo de fios de trabalhador ou sessão
- Bloqueio excessivo da base de dados
- Pressão da memória
- Outras estatísticas de espera

O alcance dos limites de recursos ou dos limites de recursos elásticos do pool denotam que o consumo de recursos disponíveis numa subscrição ou na piscina elástica ultrapassou limiares absolutos. Estas estatísticas indicam a degradação do desempenho da carga de trabalho. Um número excessivo de fios de trabalhador ou de sessão denota uma condição em que o número de fios ou sessões dos trabalhadores iniciados ultrapassou limiares absolutos. Estas estatísticas indicam a degradação do desempenho da carga de trabalho.

O bloqueio excessivo da base de dados denota uma condição em que a contagem de fechaduras numa base de dados ultrapassou limiares absolutos. Esta estatística indica uma degradação do desempenho da carga de trabalho. A pressão da memória é uma condição em que o número de fios que solicitam subsídios de memória ultrapassou um limiar absoluto. Esta estatística indica uma degradação do desempenho da carga de trabalho.

Outras estatísticas de espera indicam uma condição em que métricas diversas medidaatravés das estatísticas de espera da Loja de Consulta atravessaram um limiar absoluto. Estas estatísticas indicam a degradação do desempenho da carga de trabalho.

Após tempos de espera excessivos, dependendo dos dados disponíveis, os diagnósticos De Insights Inteligentes registam as hashes das consultas afetadas e afetadas degradadas no desempenho, detalhes das métricas que fazem com que as consultas esperem na execução, e tempo de espera medido.

## <a name="errored-requests"></a>Pedidos errados

O modelo de degradação de pedidos erradomonitoriza as consultas individuais e deteta um aumento no número de consultas que errou em comparação com o período de base. Este modelo também monitoriza exceções críticas que cruzaram limiares absolutos geridos pela Base de Dados SQL incorporada na inteligência incorporada. O sistema considera automaticamente o número de pedidos de consulta feitos na base de dados e explica quaisquer alterações de carga de trabalho no período monitorizado.

Quando o aumento medido dos pedidos errados relativos ao número global de pedidos feitos é considerado significativo para o desempenho da carga de trabalho, as consultas afetadas são sinalizadas como problemas de degradação do desempenho dos pedidos errados.

O log Insights Inteligentes produz a contagem de pedidos errados. Indica se a degradação do desempenho estava relacionada com um aumento dos pedidos errados ou a atravessar um limiar de exceção crítica monitorizado e o tempo medido da degradação do desempenho.

Se alguma das exceções críticas monitorizadas cruzar os limiares absolutos geridos pelo sistema, uma perceção inteligente é gerada com detalhes críticos de exceção.

## <a name="next-steps"></a>Passos seguintes

- Saiba como monitorizar a Base de [Dados SQL utilizando o SQL Analytics](../azure-monitor/insights/azure-sql.md).
- Saiba como resolver problemas de desempenho da Base de [Dados SQL com Insights Inteligentes](sql-database-intelligent-insights-troubleshoot-performance.md).
