---
title: Resolução de problemas de desempenho com o Intelligent Insights
description: A Intelligent Insights ajuda-o a resolver problemas na Base de Dados Azure SQL e nos problemas de desempenho geridos do Azure SQL.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: troubleshooting
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 1/14/2021
ms.openlocfilehash: 17ea6716f090144e8dfef16721bfb69dc23e9912
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589326"
---
# <a name="troubleshoot-azure-sql-database-and-azure-sql-managed-instance-performance-issues-with-intelligent-insights"></a>Resolução de problemas Azure SQL Database e Azure SQL Gerenciados Problemas de desempenho com Insights Inteligentes
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Esta página fornece informações sobre a Base de Dados Azure SQL e os problemas de desempenho da Azure SQL Managed Instance detetados através do registo de recursos [de Insights Inteligentes.](intelligent-insights-overview.md) As métricas e os registos de recursos podem ser transmitidos para [registos do Azure Monitor](../../azure-monitor/insights/azure-sql.md), [Azure Event Hubs](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs), [Azure Storage](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#stream-into-azure-storage), ou uma solução de terceiros para capacidades de alerta e relatório de DevOps personalizados.

> [!NOTE]
> Para obter um guia de resolução rápida de problemas de desempenho utilizando Insights Inteligentes, consulte o fluxo de fluxo recomendado para [resolução de problemas](intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) neste documento.
>
> Insights inteligentes é uma característica de pré-visualização, não disponível nas seguintes regiões: Europa Ocidental, Norte da Europa, EUA 1 e Leste dos EUA 1.

## <a name="detectable-database-performance-patterns"></a>Padrões de desempenho de base de dados detetáveis

A Intelligent Insights deteta automaticamente problemas de desempenho baseados em tempos de espera de execução de consultas, erros ou intervalos de tempo. As saídas de Insights Inteligentes detetaram padrões de desempenho no registo de recursos. Padrões de desempenho detetáveis são resumidos na tabela abaixo.

| Padrões de desempenho detetáveis | Base de Dados SQL do Azure | Instância Gerida do Azure SQL |
| :------------------- | ------------------- | ------------------- |
| [Atingir limites de recursos](intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | O consumo de recursos disponíveis (DTUs), linhas de trabalhadores de base de dados ou sessões de login de base de dados disponíveis na subscrição monitorizada atingiu os seus limites de recursos. Isto está a afetar o desempenho. | O consumo de recursos da CPU está a atingir os seus limites de recursos. Isto está a afetar o desempenho da base de dados. |
| [Aumento da carga de trabalho](intelligent-insights-troubleshoot-performance.md#workload-increase) | Foi detetado um aumento da carga de trabalho ou acumulação contínua de carga de trabalho na base de dados. Isto está a afetar o desempenho. | Foi detetado um aumento da carga de trabalho. Isto está a afetar o desempenho da base de dados. |
| [Pressão da memória](intelligent-insights-troubleshoot-performance.md#memory-pressure) | Os trabalhadores que solicitaram subsídios de memória têm de esperar por alocações de memória por quantidades estatisticamente significativas de tempo, ou por uma acumulação acrescida de trabalhadores que solicitaram subsídios de memória. Isto está a afetar o desempenho. | Os trabalhadores que solicitaram subsídios de memória estão à espera de alocações de memória por um período estatisticamente significativo. Isto está a afetar o desempenho da base de dados. |
| [Bloquear](intelligent-insights-troubleshoot-performance.md#locking) | Foi detetado um bloqueio excessivo na base de dados que afeta o desempenho. | Foi detetado um bloqueio excessivo na base de dados que afeta o desempenho da base de dados. |
| [AUMENTO DO MAXDOP](intelligent-insights-troubleshoot-performance.md#increased-maxdop) | O grau máximo de opção de paralelismo (MAXDOP) alterou afetando a eficiência da execução da consulta. Isto está a afetar o desempenho. | O grau máximo de opção de paralelismo (MAXDOP) alterou afetando a eficiência da execução da consulta. Isto está a afetar o desempenho. |
| [Contenção pagelatch](intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | Vários fios estão simultaneamente a tentar aceder às mesmas páginas de tampão de dados na memória, resultando num aumento dos tempos de espera e na contenção do pagelatch. Isto está a afetar o desempenho. | Vários fios estão simultaneamente a tentar aceder às mesmas páginas de tampão de dados na memória, resultando num aumento dos tempos de espera e na contenção do pagelatch. Isto está a afetar a base de dados do desempenho. |
| [Índice em Falta](intelligent-insights-troubleshoot-performance.md#missing-index) | O índice em falta foi detetado a afetar o desempenho. | O índice em falta foi detetado a afetar o desempenho da base de dados. |
| [Nova Consulta](intelligent-insights-troubleshoot-performance.md#new-query) | Foi detetada nova consulta que afeta o desempenho geral. | Foi detetada nova consulta que afeta o desempenho global da base de dados. |
| [Estatística de espera aumentada](intelligent-insights-troubleshoot-performance.md#increased-wait-statistic) | Foram detetados tempos de espera na base de dados que afetam o desempenho. | Foram detetados tempos de espera na base de dados que afetam o desempenho da base de dados. |
| [Contenção temporária](intelligent-insights-troubleshoot-performance.md#tempdb-contention) | Vários fios estão a tentar aceder ao mesmo recurso TempDB que causa um estrangulamento. Isto está a afetar o desempenho. | Vários fios estão a tentar aceder ao mesmo recurso TempDB que causa um estrangulamento. Isto está a afetar o desempenho da base de dados. |
| [Escassez de DTU de piscina elástica](intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | A escassez de eDTUs disponíveis na piscina elástica está a afetar o desempenho. | Não está disponível para Azure SQL Managed Instance uma vez que utiliza o modelo vCore. |
| [Regressão do Plano](intelligent-insights-troubleshoot-performance.md#plan-regression) | Foi detetado um novo plano, ou uma alteração na carga de trabalho de um plano existente. Isto está a afetar o desempenho. | Foi detetado um novo plano, ou uma alteração na carga de trabalho de um plano existente. Isto está a afetar o desempenho da base de dados. |
| [Alteração do valor de configuração do âmbito de dados](intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | Foi detetada uma alteração de configuração na base de dados que afeta o desempenho da base de dados. | Foi detetada uma alteração de configuração na base de dados que afeta o desempenho da base de dados. |
| [Cliente lento](intelligent-insights-troubleshoot-performance.md#slow-client) | O cliente de aplicação lenta não consegue consumir a produção da base de dados com rapidez suficiente. Isto está a afetar o desempenho. | O cliente de aplicação lenta não consegue consumir a produção da base de dados com rapidez suficiente. Isto está a afetar o desempenho da base de dados. |
| [Redução do nível de preços](intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | A ação de redução do nível de preços diminuiu os recursos disponíveis. Isto está a afetar o desempenho. | A ação de redução do nível de preços diminuiu os recursos disponíveis. Isto está a afetar o desempenho da base de dados. |

> [!TIP]
> Para uma otimização contínua do desempenho das bases de dados, ative [a sintonização automática](automatic-tuning-overview.md). Esta funcionalidade de inteligência incorporada monitoriza continuamente a sua base de dados, afina automaticamente índices e aplica correções de planos de execução de consultas.
>

A secção seguinte descreve padrões de desempenho detetáveis com mais detalhes.

## <a name="reaching-resource-limits"></a>Atingir limites de recursos

### <a name="what-is-happening"></a>O que é que está a acontecer

Este padrão de desempenho detetável combina problemas de desempenho que estão relacionados com a obtenção de limites de recursos disponíveis, limites de trabalho e limites de sessão. Após a deteção deste problema de desempenho, um campo de descrição do registo de diagnóstico indica se o problema de desempenho está relacionado com os limites de recursos, trabalhadores ou de sessão.

Os recursos na Base de Dados Azure SQL são tipicamente referidos a recursos [DTU](service-tiers-dtu.md) ou [vCore,](service-tiers-vcore.md) e os recursos em Azure SQL Managed Instance são referidos como recursos vCore. O padrão de atingir os limites de recursos é reconhecido quando detetado a degradação do desempenho da consulta é causada por atingir qualquer um dos limites de recursos medidos.

A sessão limita o recurso denota o número de logins simultâneos disponíveis na base de dados. Este padrão de desempenho é reconhecido quando as aplicações que estão ligadas às bases de dados atingiram o número de logins simultâneos disponíveis na base de dados. Se as aplicações tentarem utilizar mais sessões do que estão disponíveis numa base de dados, o desempenho da consulta é afetado.

Atingir os limites dos trabalhadores é um caso específico de atingir limites de recursos porque os trabalhadores disponíveis não são contabilizados no uso de DTU ou vCore. Atingir os limites dos trabalhadores numa base de dados pode causar o aumento dos tempos de espera específicos dos recursos, o que resulta na degradação do desempenho da consulta.

### <a name="troubleshooting"></a>Resolução de problemas

Os diagnósticos registam as saídas de consultas que afetaram as percentagens de desempenho e consumo de recursos. Pode utilizar esta informação como ponto de partida para otimizar a carga de trabalho da sua base de dados. Em particular, pode otimizar as consultas que afetam a degradação do desempenho adicionando índices. Ou pode otimizar aplicações com uma distribuição de carga de trabalho mais uniforme. Se não conseguir reduzir cargas de trabalho ou fazer otimizações, considere aumentar o nível de preços da subscrição da sua base de dados para aumentar a quantidade de recursos disponíveis.

Se tiver atingido os limites de sessão disponíveis, pode otimizar as suas aplicações reduzindo o número de logins feitos na base de dados. Se não conseguir reduzir o número de logins das suas aplicações para a base de dados, considere aumentar o nível de preços da subscrição da sua base de dados. Ou pode dividir e mover a sua base de dados em várias bases de dados para uma distribuição de carga de trabalho mais equilibrada.

Para obter mais sugestões sobre a resolução dos limites da sessão, consulte [Como lidar com os limites dos logins máximos.](/archive/blogs/latam/how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins) Consulte [a visão geral dos limites de recursos num servidor](resource-limits-logical-server.md) para obter informações sobre limites nos níveis de servidor e subscrição.

## <a name="workload-increase"></a>Aumento da carga de trabalho

### <a name="what-is-happening"></a>O que é que está a acontecer

Este padrão de desempenho identifica problemas causados por um aumento da carga de trabalho ou, na sua forma mais severa, um amontoado de carga de trabalho.

Esta deteção é feita através de uma combinação de várias métricas. A métrica básica medida é detetar um aumento da carga de trabalho em comparação com a linha de base de carga de trabalho anterior. A outra forma de deteção baseia-se na medição de um grande aumento de fios de trabalhadores ativos que é grande o suficiente para afetar o desempenho da consulta.

Na sua forma mais severa, a carga de trabalho pode acumular-se continuamente devido à incapacidade de uma base de dados para lidar com a carga de trabalho. O resultado é um tamanho de carga de trabalho em crescimento contínuo, que é a condição de acumulação de carga de trabalho. Devido a esta condição, o tempo que a carga de trabalho espera pela execução aumenta. Esta condição representa um dos mais graves problemas de desempenho na base de dados. Esta questão é detetada através da monitorização do aumento do número de fios de trabalhador abortados.

### <a name="troubleshooting"></a>Resolução de problemas

Os diagnósticos registam o número de consultas cuja execução aumentou e o haxixe de consulta da consulta com a maior contribuição para o aumento da carga de trabalho. Pode utilizar esta informação como ponto de partida para otimizar a carga de trabalho. A consulta identificada como o maior contribuinte para o aumento da carga de trabalho é especialmente útil como o seu ponto de partida.

Pode considerar distribuir as cargas de trabalho de forma mais homogésa na base de dados. Considere otimizar a consulta que está a afetar o desempenho adicionando índices. Também pode distribuir a sua carga de trabalho entre várias bases de dados. Se estas soluções não forem possíveis, considere aumentar o nível de preços da subscrição da sua base de dados para aumentar a quantidade de recursos disponíveis.

## <a name="memory-pressure"></a>Pressão da memória

### <a name="what-is-happening"></a>O que é que está a acontecer

Este padrão de desempenho indica uma degradação no desempenho atual da base de dados causada pela pressão da memória, ou na sua forma mais severa, uma condição de acumulação de memória, em comparação com a linha de base de desempenho de sete dias passada.

A pressão da memória denota uma condição de desempenho em que há um grande número de fios de trabalhador solicitando subsídios de memória. O elevado volume causa uma elevada condição de utilização da memória em que a base de dados não consegue alocar eficientemente a memória a todos os trabalhadores que a solicitem. Uma das razões mais comuns para esta questão está relacionada com a quantidade de memória disponível na base de dados, por um lado. Por outro lado, o aumento da carga de trabalho provoca o aumento dos fios dos trabalhadores e a pressão da memória.

A forma mais severa de pressão da memória é a condição de acumulação de memória. Esta condição indica que um maior número de fios de trabalhadores estão a solicitar subsídios de memória do que há consultas que libertam a memória. Este número de fios de trabalhador que solicitam subsídios de memória também pode estar continuamente a aumentar (acumulando-se) porque o motor da base de dados não consegue alocar a memória de forma suficientemente eficiente para satisfazer a procura. A condição de acumulação de memória representa um dos mais graves problemas de desempenho da base de dados.

### <a name="troubleshooting"></a>Resolução de problemas

O registo de diagnóstico sai do objeto de memória armazena detalhes com o funcionário (isto é, fio do trabalhador) marcado como a razão mais alta para o uso de alta memória e selos de tempo relevantes. Pode utilizar esta informação como base para a resolução de problemas.

Pode otimizar ou remover consultas relacionadas com os funcionários com o uso de memória mais elevado. Também pode certificar-se de que não está a consultar dados que não pretende utilizar. A boa prática é usar sempre uma cláusula WHERE nas suas consultas. Além disso, recomendamos que crie índices não agrupados para procurar os dados em vez de digitalizá-lo.

Também pode reduzir a carga de trabalho otimizando ou distribuindo-a através de várias bases de dados. Ou pode distribuir a sua carga de trabalho entre várias bases de dados. Se estas soluções não forem possíveis, considere aumentar o nível de preços da sua base de dados para aumentar a quantidade de recursos de memória disponíveis na base de dados.

Para obter sugestões adicionais de resolução de problemas, consulte [a meditação de bolsas de memória memory: O misterioso consumidor de memória SQL Server com muitos nomes](https://techcommunity.microsoft.com/t5/sql-server-support/memory-grants-meditation-the-mysterious-sql-server-memory/ba-p/333994).

## <a name="locking"></a>Bloquear

### <a name="what-is-happening"></a>O que é que está a acontecer

Este padrão de desempenho indica uma degradação no desempenho atual da base de dados em que é detetado um bloqueio excessivo da base de dados em comparação com a linha de base de desempenho de sete dias passada.

Nos RDBMS modernos, o bloqueio é essencial para a implementação de sistemas multi-leitores em que o desempenho é maximizado através da execução de múltiplos trabalhadores simultâneos e transações paralelas de bases de dados sempre que possível. O bloqueio neste contexto refere-se ao mecanismo de acesso incorporado no qual apenas uma única transação pode aceder exclusivamente às linhas, páginas, tabelas e ficheiros que são necessários e não competir com outra transação de recursos. Quando a transação que bloqueou os recursos para utilização é feita com eles, o bloqueio a esses recursos é liberado, o que permite que outras transações acedam aos recursos necessários. Para obter mais informações sobre o bloqueio, consulte [Lock no motor da base de dados](/previous-versions/sql/sql-server-2008-r2/ms190615(v=sql.105)).

Se as transações executadas pelo motor SQL aguardam por longos períodos de tempo para aceder aos recursos bloqueados para utilização, este tempo de espera provoca o abrandamento do desempenho da execução da carga de trabalho.

### <a name="troubleshooting"></a>Resolução de problemas

Os resultados de registo de diagnóstico bloqueiam detalhes que pode utilizar como base para a resolução de problemas. Pode analisar as consultas de bloqueio relatadas, ou seja, as consultas que introduzem a degradação do desempenho do bloqueio, e removê-las. Em alguns casos, poderá conseguir otimizar as consultas de bloqueio.

A forma mais simples e segura de mitigar o problema é manter as transações curtas e reduzir a pegada de bloqueio das consultas mais caras. Pode dividir um grande lote de operações em operações menores. As boas práticas consistem em reduzir a pegada de bloqueio de consulta, tornando a consulta o mais eficiente possível. Reduza as grandes análises porque aumentam as probabilidades de impasse e afetam negativamente o desempenho global da base de dados. Para consultas identificadas que causem bloqueio, pode criar novos índices ou adicionar colunas ao índice existente para evitar as digitalizações da tabela.

Para mais sugestões, consulte:
- [Compreender e resolver problemas de bloqueio do Azure SQL](understand-resolve-blocking.md)
- [Como resolver problemas de bloqueio que são causados pela escalada de bloqueio no SQL Server](https://support.microsoft.com/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in)

## <a name="increased-maxdop"></a>AUMENTO DO MAXDOP

### <a name="what-is-happening"></a>O que é que está a acontecer

Este padrão de desempenho detetável indica uma condição em que um plano de execução de consulta escolhido foi mais paralelo do que deveria ter sido. O otimizador de consultas pode melhorar o desempenho da carga de trabalho executando consultas paralelamente para acelerar as coisas sempre que possível. Em alguns casos, os trabalhadores paralelos que processam uma consulta passam mais tempo à espera uns dos outros para sincronizar e fundir resultados em comparação com a execução da mesma consulta com menos trabalhadores paralelos, ou mesmo em alguns casos em comparação com um único fio de trabalhador.

O sistema de peritos analisa o desempenho atual da base de dados em comparação com o período de base. Determina se uma consulta em execução anterior está a decorrer mais lentamente do que antes porque o plano de execução de consultas está mais paralelo do que deveria ser.

A opção de configuração do servidor MAXDOP é usada para controlar quantos núcleos de CPU podem ser usados para executar a mesma consulta em paralelo.

### <a name="troubleshooting"></a>Resolução de problemas

Os diagnósticos registam as saídas de dados relacionadas com consultas para as quais a duração da execução aumentou porque foram mais paralelas do que deveriam ter sido. O registo também produz tempos de espera CXP. Desta vez representa o tempo que um único fio organizador/coordenador (thread 0) está à espera que todos os outros fios terminem antes de fundir os resultados e seguir em frente. Além disso, os diagnósticos registam os tempos de espera que as consultas de mau desempenho aguardavam na execução em geral. Pode utilizar esta informação como base para a resolução de problemas.

Primeiro, otimizar ou simplificar consultas complexas. A boa prática é dividir os longos lotes em pequenos. Além disso, certifique-se de que criou índices para suportar as suas consultas. Também pode aplicar manualmente o grau máximo de paralelismo (MAXDOP) para uma consulta que foi sinalizada como um mau desempenho. Para configurar esta operação utilizando o T-SQL, consulte [configurar a opção de configuração do servidor MAXDOP](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

Definindo a opção de configuração do servidor MAXDOP para zero (0) como um valor predefinido denota que a base de dados pode usar todos os núcleos de CPU disponíveis para paralelizar fios para executar uma única consulta. A definição maxdop para um (1) denota que apenas um núcleo pode ser usado para uma única execução de consulta. Em termos práticos, isto significa que o paralelismo está desligado. Dependendo da base de caso por caso, dos núcleos disponíveis na base de dados e da informação de registo de diagnósticos, pode sintonizar a opção MAXDOP com o número de núcleos utilizados para a execução de consultas paralelas que possam resolver o problema no seu caso.

## <a name="pagelatch-contention"></a>Contenção pagelatch

### <a name="what-is-happening"></a>O que é que está a acontecer

Este padrão de desempenho indica a degradação atual do desempenho da carga de trabalho da base de dados devido à contenção do pagelatch em comparação com a linha de base de carga de trabalho de sete dias passada.

Os fechos são mecanismos leves de sincronização utilizados para permitir a multi-leitura. Garantem a consistência das estruturas na memória que incluem índices, páginas de dados e outras estruturas internas.

Existem muitos tipos de fechos disponíveis. Para fins de simplicidade, os fechos tampão são usados para proteger páginas de memória na piscina tampão. Os fechos iO são usados para proteger páginas ainda não carregadas na piscina tampão. Sempre que os dados são escritos ou lidos a partir de uma página na piscina tampão, um fio de trabalhador precisa adquirir um trinco tampão para a página primeiro. Sempre que um fio de trabalhador tenta aceder a uma página que ainda não está disponível no conjunto de tampão de memória, é feito um pedido de IO para carregar as informações necessárias do armazenamento. Esta sequência de eventos indica uma forma mais severa de degradação do desempenho.

A contenção nos fechos de página ocorre quando vários fios tentam simultaneamente adquirir fechos na mesma estrutura na memória, o que introduz um maior tempo de espera para consultar a execução. No caso de contenção de IO pagelatch, quando os dados precisam de ser acedidos a partir do armazenamento, este tempo de espera é ainda maior. Pode afetar consideravelmente o desempenho da carga de trabalho. A contenção pagelatch é o cenário mais comum de fios à espera uns dos outros e a competir por recursos em vários sistemas de CPU.

### <a name="troubleshooting"></a>Resolução de problemas

Os resultados do registo de diagnósticos de páginalatch detalhes de contenção. Pode utilizar esta informação como base para a resolução de problemas.

Como um pagelatch é um mecanismo de controlo interno, determina automaticamente quando deve utilizá-los. As decisões de aplicação, incluindo o design de esquema, podem afetar o comportamento pagelatch devido ao comportamento determinístico dos fechos.

Um método para lidar com a contenção do fecho é substituir uma chave de índice sequencial por uma chave não sequencial para distribuir uniformemente os inserções através de uma gama de índices. Tipicamente, uma coluna líder no índice distribui a carga de trabalho proporcionalmente. Outro método a considerar é a divisão de mesa. Criar um esquema de partição de haxixe com uma coluna computada sobre uma mesa dividida é uma abordagem comum para atenuar a contenção excessiva do trinco. No caso de contenção de IO pagelatch, a introdução de índices ajuda a atenuar esta questão de desempenho.

Para obter mais informações, consulte [Diagnosticar e resolver a contenção do fecho no SQL Server](http://databaser.net/moniwiki/pds/PerformanceTuning/SQLServerLatchContention.pdf) (download PDF).

## <a name="missing-index"></a>Índice em falta

### <a name="what-is-happening"></a>O que é que está a acontecer

Este padrão de desempenho indica a degradação atual do desempenho da carga de trabalho da base de dados em comparação com a linha de base de sete dias passada devido a um índice em falta.

Um índice é usado para acelerar o desempenho das consultas. Fornece acesso rápido aos dados dos quadros, reduzindo o número de páginas de conjunto de dados que precisam de ser visitadas ou digitalizadas.

As consultas específicas que causaram a degradação do desempenho são identificadas através desta deteção para a qual a criação de índices seria benéfica para o desempenho.

### <a name="troubleshooting"></a>Resolução de problemas

Os diagnósticos registam a consulta de hashes para as consultas que foram identificadas para afetar o desempenho da carga de trabalho. Pode construir índices para estas consultas. Também pode otimizar ou remover estas consultas se não forem necessárias. Uma boa prática de desempenho é evitar consultar dados que não utiliza.

> [!TIP]
> Sabia que a inteligência incorporada pode gerir automaticamente os índices de melhor desempenho para as suas bases de dados?
>
> Para uma otimização contínua do desempenho, recomendamos que ative a [sintonização automática](automatic-tuning-overview.md). Esta funcionalidade única de inteligência incorporada monitoriza continuamente a sua base de dados e sintoniza automaticamente e cria índices para as suas bases de dados.
>

## <a name="new-query"></a>Nova consulta

### <a name="what-is-happening"></a>O que é que está a acontecer

Este padrão de desempenho indica que é detetada uma nova consulta que está a ter um desempenho fraco e que afeta o desempenho da carga de trabalho em comparação com a linha de base de desempenho de sete dias.

Escrever uma consulta de bom desempenho às vezes pode ser uma tarefa desafiadora. Para obter mais informações sobre consultas de escrita, consulte [consultas de Escrita SQL.](/previous-versions/sql/sql-server-2005/express-administrator/bb264565(v=sql.90)) Para otimizar o desempenho da consulta existente, consulte [a sintonização de consulta](/previous-versions/sql/sql-server-2008-r2/ms176005(v=sql.105)).

### <a name="troubleshooting"></a>Resolução de problemas

Os diagnósticos registam informações até duas novas consultas mais consumidas por CPU, incluindo as suas consultas. Como a consulta detetada afeta o desempenho da carga de trabalho, pode otimizar a sua consulta. A boa prática é recuperar apenas os dados que precisa de utilizar. Recomendamos também a utilização de consultas com uma cláusula WHERE. Recomendamos também que simplifique consultas complexas e as separe em consultas menores. Outra boa prática é dividir grandes consultas de lote em consultas de lote mais pequenas. Introduzir índices para novas consultas é tipicamente uma boa prática para mitigar esta questão de desempenho.

Na Base de Dados Azure SQL, considere a utilização [de Insight de Desempenho de Consulta](query-performance-insight-use.md).

## <a name="increased-wait-statistic"></a>Aumento da estatística da espera

### <a name="what-is-happening"></a>O que é que está a acontecer

Este padrão de desempenho detetável indica uma degradação do desempenho da carga de trabalho na qual são identificadas consultas de baixo desempenho em comparação com a linha de base de carga de trabalho de sete dias passada.

Neste caso, o sistema não pode classificar as consultas de mau desempenho em quaisquer outras categorias de desempenho detetáveis padrão, mas detetou a estatística de espera responsável pela regressão. Por isso, considera-as como consultas com *estatísticas de espera acrescidas,* onde a estatística de espera responsável pela regressão também está exposta.

### <a name="troubleshooting"></a>Resolução de problemas

Os diagnósticos registam informações sobre o aumento dos detalhes do tempo de espera e os hashes de consulta das consultas afetadas.

Como o sistema não conseguiu identificar com sucesso a causa principal das consultas de mau desempenho, a informação de diagnóstico é um bom ponto de partida para a resolução manual de problemas. Pode otimizar o desempenho destas consultas. Uma boa prática é recolher apenas dados que você precisa usar e simplificar e decompor consultas complexas em consultas menores.

Para obter mais informações sobre a otimização do desempenho da consulta, consulte [afinação de consultas](/previous-versions/sql/sql-server-2008-r2/ms176005(v=sql.105)).

## <a name="tempdb-contention"></a>Contenção temporária

### <a name="what-is-happening"></a>O que é que está a acontecer

Este padrão de desempenho detetável indica uma condição de desempenho da base de dados na qual existe um estrangulamento de fios que tentam aceder aos recursos temporários. (Esta condição não está relacionada com IO.) O cenário típico para esta questão de desempenho são centenas de consultas simultâneas que todos criam, usam e depois largam pequenas tabelas temporárias. O sistema detetou que o número de consultas simultâneas utilizando as mesmas tabelas temporárias aumentou com significado estatístico suficiente para afetar o desempenho da base de dados em comparação com a linha de base de desempenho dos últimos sete dias.

### <a name="troubleshooting"></a>Resolução de problemas

Os diagnósticos registam detalhes de contenção temporária. Pode utilizar a informação como ponto de partida para a resolução de problemas. Há duas coisas que pode perseguir para aliviar este tipo de discórdia e aumentar a produção da carga de trabalho global: Pode parar de usar as tabelas temporárias. Também pode utilizar tabelas otimizadas para a memória.

Para obter mais informações, consulte [Introdução às tabelas otimizadas para a memória.](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables)

## <a name="elastic-pool-dtu-shortage"></a>Escassez de DTU de piscina elástica

### <a name="what-is-happening"></a>O que é que está a acontecer

Este padrão de desempenho detetável indica uma degradação no desempenho atual da carga de trabalho da base de dados em comparação com a linha de base de sete dias passada. É devido à escassez de DTUs disponíveis na piscina elástica da sua assinatura.

[Os recursos de piscina elástica Azure](elastic-pool-overview.md) são usados como um conjunto de recursos disponíveis partilhados entre várias bases de dados para fins de escala. Quando os recursos eDTU disponíveis na sua piscina elástica não são suficientemente grandes para suportar todas as bases de dados da piscina, um problema de desempenho de falta de DTU de piscina elástica é detetado pelo sistema.

### <a name="troubleshooting"></a>Resolução de problemas

O registo de diagnósticos informações sobre as saídas no pool elástico, lista as bases de dados de consumo de DTU superiores, e fornece uma percentagem do DTU da piscina usado pela base de dados mais consumida.

Como esta condição de desempenho está relacionada com várias bases de dados usando o mesmo conjunto de eDTUs na piscina elástica, os passos de resolução de problemas concentram-se nas bases de dados de consumo de DTU. Pode reduzir a carga de trabalho nas bases de dados mais consumidas, o que inclui a otimização das consultas mais consumidas nessas bases de dados. Também pode garantir que não está a consultar dados que não utiliza. Outra abordagem é otimizar as aplicações utilizando as principais bases de dados de consumo de DTU e redistribuir a carga de trabalho entre várias bases de dados.

Se a redução e otimização da carga de trabalho atual nas suas bases de dados de consumo de DTU não forem possíveis, considere aumentar o seu nível de preços de piscina elástica. Este aumento resulta no aumento dos DTUs disponíveis na piscina elástica.

## <a name="plan-regression"></a>Regressão do plano

### <a name="what-is-happening"></a>O que é que está a acontecer

Este padrão de desempenho detetável denota uma condição em que a base de dados utiliza um plano de execução de consulta sub-ideais. O plano subóptenal normalmente causa uma execução de consulta aumentada, o que leva a tempos de espera mais longos para as consultas atuais e outras.

O motor de base de dados determina o plano de execução de consulta com o menor custo para uma execução de consulta. À medida que o tipo de consultas e cargas de trabalho mudam, por vezes os planos existentes já não são eficientes, ou talvez o motor da base de dados não tenha feito uma boa avaliação. Por uma questão de correção, os planos de execução de consultas podem ser forçados manualmente.

Este padrão de desempenho detetável combina três casos diferentes de regressão do plano: regressão de novo plano, regressão de plano antigo e planos existentes mudaram a carga de trabalho. O tipo particular de regressão do plano que ocorreu é fornecido na propriedade *de detalhes* no log de diagnóstico.

A nova condição de regressão do plano refere-se a um estado em que o motor da base de dados começa a executar um novo plano de execução de consultas que não é tão eficiente como o plano antigo. A antiga condição de regressão do plano refere-se ao estado quando o motor da base de dados muda de usar um novo plano mais eficiente para o plano antigo, que não é tão eficiente como o novo plano. Os planos existentes mudaram a regressão da carga de trabalho refere-se ao estado em que os antigos e os novos planos alternam continuamente, com o saldo a ir mais para o plano de mau desempenho.

Para obter mais informações sobre regressões de planos, veja [o que é a regressão do plano no SQL Server?](/archive/blogs/sqlserverstorageengine/what-is-plan-regression-in-sql-server)

### <a name="troubleshooting"></a>Resolução de problemas

Os diagnósticos registam as saídas de consultas, identificação de bom plano, identificação de plano mau e iDs de consulta. Pode utilizar esta informação como base para a resolução de problemas.

Pode analisar qual o plano que melhor executa as suas consultas específicas que pode identificar com os hashes de consulta fornecidos. Depois de determinar qual o plano que funciona melhor para as suas consultas, pode forçá-lo manualmente.

Para obter mais informações, consulte [Como o SQL Server impede regressões de planos](/archive/blogs/sqlserverstorageengine/you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions).

> [!TIP]
> Sabia que a funcionalidade de inteligência incorporada pode gerir automaticamente os planos de execução de consultas com melhor desempenho para as suas bases de dados?
>
> Para uma otimização contínua do desempenho, recomendamos que ative a [sintonização automática](automatic-tuning-overview.md). Esta funcionalidade de inteligência incorporada monitoriza continuamente a sua base de dados e sintoniza automaticamente e cria planos de execução de consultas de melhor desempenho para as suas bases de dados.

## <a name="database-scoped-configuration-value-change"></a>Alteração do valor de configuração do âmbito de dados

### <a name="what-is-happening"></a>O que é que está a acontecer

Este padrão de desempenho detetável indica uma condição em que uma alteração na configuração de âmbito de base de dados causa regressão de desempenho que é detetada em comparação com o comportamento de carga de trabalho de base de dados de sete dias passados. Este padrão denota que uma alteração recente feita na configuração de âmbito de base de dados não parece ser benéfica para o desempenho da sua base de dados.

As alterações de configuração com âmbito de dados podem ser definidas para cada base de dados individual. Esta configuração é utilizada caso a caso para otimizar o desempenho individual da sua base de dados. As seguintes opções podem ser configuradas para cada base de dados: MAXDOP, LEGACY_CARDINALITY_ESTIMATION, PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES e CLEAR PROCEDURE_CACHE.

### <a name="troubleshooting"></a>Resolução de problemas

Os diagnósticos registam alterações de configuração de base de dados que foram feitas recentemente que causaram a degradação do desempenho em comparação com o comportamento anterior de carga de trabalho de sete dias. Pode reverter as alterações de configuração para os valores anteriores. Também pode sintonizar valor por valor até que o nível de desempenho desejado seja atingido. Pode copiar valores de configuração de âmbito de base de dados a partir de uma base de dados semelhante com desempenho satisfatório. Se não conseguir resolver o desempenho, volte aos valores predefinidos e tente afinar a partir desta linha de base.

Para obter mais informações sobre a otimização da configuração de bases de dados e sintaxe T-SQL na alteração da configuração, consulte alterar a [configuração de âmbito de base de dados (Transact-SQL)](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql).

## <a name="slow-client"></a>Cliente lento

### <a name="what-is-happening"></a>O que é que está a acontecer

Este padrão de desempenho detetável indica uma condição em que o cliente que utiliza a base de dados não pode consumir a saída da base de dados tão rapidamente quanto a base de dados envia os resultados. Como a base de dados não está a armazenar resultados das consultas executadas num tampão, abranda e espera que o cliente consuma as saídas de consulta transmitidas antes de prosseguir. Esta condição também pode estar relacionada com uma rede que não é suficientemente rápida para transmitir saídas da base de dados para o cliente que consome.

Esta condição só é gerada se for detetada uma regressão de desempenho em comparação com o comportamento de carga de trabalho da base de dados de sete dias passados. Este problema de desempenho só é detetado se ocorrer uma degradação do desempenho estatisticamente significativa em comparação com o comportamento de desempenho anterior.

### <a name="troubleshooting"></a>Resolução de problemas

Este padrão de desempenho detetável indica uma condição do lado do cliente. A resolução de problemas é necessária na aplicação do lado do cliente ou na rede do lado do cliente. Os diagnósticos registam as saídas de consultas e tempos de espera que parecem estar mais à espera para que o cliente os consuma nas últimas duas horas. Pode utilizar esta informação como base para a resolução de problemas.

Pode otimizar o desempenho da sua aplicação para consumo destas consultas. Também pode considerar possíveis problemas de latência na rede. Como o problema da degradação do desempenho foi baseado na mudança na linha de base de desempenho de sete dias, pode investigar se alterações recentes da aplicação ou da condição de rede causaram este evento de regressão de desempenho.

## <a name="pricing-tier-downgrade"></a>Redução do nível de preços

### <a name="what-is-happening"></a>O que é que está a acontecer

Este padrão de desempenho detetável indica uma condição em que o nível de preços da subscrição da sua base de dados foi desclassificado. Devido à redução dos recursos (DTUs) disponíveis para a base de dados, o sistema detetou uma queda no desempenho atual da base de dados em comparação com a linha de base de sete dias anteriores.

Além disso, poderia haver uma condição em que o nível de preços da sua subscrição de base de dados foi desclassificado e, em seguida, atualizado para um nível mais alto num curto espaço de tempo. A deteção desta degradação temporária do desempenho é desatada na secção de detalhes do registo de diagnósticos como uma degradação e atualização do nível de preços.

### <a name="troubleshooting"></a>Resolução de problemas

Se reduziu o seu nível de preços e, portanto, os DTUs disponíveis, e está satisfeito com o desempenho, não há nada que precise fazer. Se reduziu o seu nível de preços e está insatisfeito com o desempenho da sua base de dados, reduza as cargas de trabalho da sua base de dados ou considere aumentar o nível de preços para um nível mais elevado.

## <a name="recommended-troubleshooting-flow"></a>Fluxo recomendado de resolução de problemas

 Siga o fluxograma para obter uma abordagem recomendada para resolver problemas de desempenho utilizando Insights Inteligentes.

Aceda a Insights Inteligentes através do portal Azure indo ao Azure SQL Analytics. Tente localizar o alerta de desempenho e selecione-o. Identifique o que está a acontecer na página de deteções. Observe a análise da causa raiz fornecida do problema, texto de consulta, tendências de tempo de consulta e evolução de incidentes. Tente resolver o problema utilizando a recomendação de Insights Inteligentes para atenuar a questão do desempenho.

[![Gráfico de fluxo de resolução de problemas](./media/intelligent-insights-troubleshoot-performance/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> Selecione o fluxograma para descarregar uma versão PDF.

Insights Inteligentes geralmente precisam de uma hora de tempo para realizar a análise da causa raiz do problema de desempenho. Se não conseguir localizar o seu problema em Insights Inteligentes e é fundamental para si, utilize a Loja de Consultas para identificar manualmente a causa principal do problema de desempenho. (Normalmente, estas questões têm menos de uma hora de idade.) Para obter mais informações, consulte [o desempenho do Monitor utilizando a Loja de Consultas.](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)

## <a name="next-steps"></a>Passos seguintes

- Aprenda [conceitos de Insights Inteligentes.](intelligent-insights-overview.md)
- Utilize o [registo de diagnósticos de desempenho de Insights Inteligentes](intelligent-insights-use-diagnostics-log.md).
- Monitor utilizando [a Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).
- Aprenda a [recolher e consumir dados de registo a partir dos seus recursos Azure](../../azure-monitor/essentials/platform-logs-overview.md).