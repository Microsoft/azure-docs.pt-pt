---
title: Resolução de problemas de desempenho com o Intelligent Insights
description: A Intelligent Insights ajuda-o a resolver problemas na Base de Dados Azure SQL e nos problemas de desempenho da Instância Gerida azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 2008dd5e1e583a99756f62bc25f27e8f9832646e
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84045607"
---
# <a name="troubleshoot-azure-sql-database-and-azure-sql-managed-instance-performance-issues-with-intelligent-insights"></a>Problemas Azure SQL Database e Problemas de Desempenho geridos azure SQL com Insights Inteligentes
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Esta página fornece informações sobre os problemas de desempenho do Azure SQL E do Azure SQL Managed Instance detetados através do registo de recursos [Intelligent Insights.](intelligent-insights-overview.md) As métricas e registos de recursos podem ser transmitidos para [registos do Monitor Azure,](../../azure-monitor/insights/azure-sql.md)Hubs de [Eventos Azure,](../../azure-monitor/platform/resource-logs-stream-event-hubs.md) [Armazenamento Azure,](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#stream-into-azure-storage)ou uma solução de terceiros para devOps personalizados alertando e reportando capacidades.

> [!NOTE]
> Para obter um guia rápido de resolução de problemas de desempenho utilizando Insights Inteligentes, consulte o fluxograma de fluxo de resolução de [problemas recomendado](intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) neste documento.

## <a name="detectable-database-performance-patterns"></a>Padrões de desempenho detetáveis da base de dados

A Intelligent Insights deteta automaticamente problemas de desempenho baseados em tempos de espera de execução de consulta, erros ou intervalos. Smart Insights detetou padrões de desempenho para o registo de recursos. Os padrões de desempenho detetáveis são resumidos na tabela abaixo.

| Padrões de desempenho detetáveis | Base de Dados SQL do Azure | Instância Gerida do Azure SQL |
| :------------------- | ------------------- | ------------------- |
| [Atingir limites de recursos](intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | O consumo de recursos disponíveis (DTUs), linhas de trabalho de base de dados ou sessões de login disponíveis na subscrição monitorizada atingiram os seus limites de recursos. Isto está a afetar o desempenho. | O consumo de recursos da CPU está a atingir os seus limites de recursos. Isto está a afetar o desempenho da base de dados. |
| [Aumento da carga de trabalho](intelligent-insights-troubleshoot-performance.md#workload-increase) | Foi detetado um aumento da carga de trabalho ou acumulação contínua de carga de trabalho na base de dados. Isto está a afetar o desempenho. | O aumento da carga de trabalho foi detetado. Isto está a afetar o desempenho da base de dados. |
| [Pressão da memória](intelligent-insights-troubleshoot-performance.md#memory-pressure) | Os trabalhadores que solicitaram subsídios de memória têm de esperar por dotações de memória por quantidades estatisticamente significativas de tempo, ou existe um aumento da acumulação de trabalhadores que solicitaram subsídios de memória. Isto está a afetar o desempenho. | Os trabalhadores que solicitaram subsídios de memória aguardam a atribuição de memória por um período de tempo estatisticamente significativo. Isto está a afetar o desempenho da base de dados. |
| [Bloquear](intelligent-insights-troubleshoot-performance.md#locking) | Foi detetado um bloqueio excessivo na base de dados que afeta o desempenho. | Foi detetado um bloqueio excessivo na base de dados que afeta o desempenho da base de dados. |
| [MAXDOP aumentado](intelligent-insights-troubleshoot-performance.md#increased-maxdop) | O grau máximo de opção de paralelismo (MAXDOP) alterou-se afetando a eficiência da execução da consulta. Isto está a afetar o desempenho. | O grau máximo de opção de paralelismo (MAXDOP) alterou-se afetando a eficiência da execução da consulta. Isto está a afetar o desempenho. |
| [Contenção de Pagelatch](intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | Vários fios estão simultaneamente a tentar aceder às mesmas páginas tampão de dados na memória, resultando num aumento dos tempos de espera e na contenção do pagelatch. Isto está a afetar o desempenho. | Vários fios estão simultaneamente a tentar aceder às mesmas páginas tampão de dados na memória, resultando num aumento dos tempos de espera e na contenção do pagelatch. Isto está a afetar a base de dados do desempenho. |
| [Índice em falta](intelligent-insights-troubleshoot-performance.md#missing-index) | O índice desaparecido foi detetado afetando o desempenho. | O índice desaparecido foi detetado afetando o desempenho da base de dados. |
| [Nova Consulta](intelligent-insights-troubleshoot-performance.md#new-query) | Foi detetada uma nova consulta que afeta o desempenho geral. | Foi detetada uma nova consulta que afeta o desempenho global da base de dados. |
| [Estatística de espera aumentada](intelligent-insights-troubleshoot-performance.md#increased-wait-statistic) | Foram detetados tempos de espera na base de dados que afetam o desempenho. | Foram detetados tempos de espera na base de dados que afetam o desempenho da base de dados. |
| [Contenção TempDB](intelligent-insights-troubleshoot-performance.md#tempdb-contention) | Vários fios estão a tentar aceder ao mesmo recurso TempDB causando um estrangulamento. Isto está a afetar o desempenho. | Vários fios estão a tentar aceder ao mesmo recurso TempDB causando um estrangulamento. Isto está a afetar o desempenho da base de dados. |
| [Escassez de DTU da piscina elástica](intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | A escassez de EDTUs disponíveis na piscina elástica está a afetar o desempenho. | Não disponível para o Azure SQL Managed Instance, uma vez que utiliza o modelo vCore. |
| [Plano regressão](intelligent-insights-troubleshoot-performance.md#plan-regression) | Foi detetado um novo plano, ou uma alteração da carga de trabalho de um plano existente. Isto está a afetar o desempenho. | Foi detetado um novo plano, ou uma alteração da carga de trabalho de um plano existente. Isto está a afetar o desempenho da base de dados. |
| [Alteração do valor da configuração com um espaço de base de dados](intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | Foi detetada uma alteração de configuração na base de dados que afeta o desempenho da base de dados. | Foi detetada uma alteração de configuração na base de dados que afeta o desempenho da base de dados. |
| [Cliente lento](intelligent-insights-troubleshoot-performance.md#slow-client) | O cliente de aplicação lenta é incapaz de consumir a saída da base de dados com rapidez suficiente. Isto está a afetar o desempenho. | O cliente de aplicação lenta é incapaz de consumir a saída da base de dados com rapidez suficiente. Isto está a afetar o desempenho da base de dados. |
| [Desvalorização do nível de preços](intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | A ação de desvalorização do nível de preços diminuiu os recursos disponíveis. Isto está a afetar o desempenho. | A ação de desvalorização do nível de preços diminuiu os recursos disponíveis. Isto está a afetar o desempenho da base de dados. |

> [!TIP]
> Para uma otimização contínua do desempenho das bases de dados, ative a [sintonização automática.](automatic-tuning-overview.md) Esta função de inteligência incorporada monitoriza continuamente a sua base de dados, afina automaticamente os índices e aplica correções do plano de execução de consultas.
>

A secção seguinte descreve padrões de desempenho detetáveis com mais detalhes.

## <a name="reaching-resource-limits"></a>Atingir limites de recursos

### <a name="what-is-happening"></a>O que é que está a acontecer

Este padrão de desempenho detetável combina problemas de desempenho relacionados com o alcance dos limites de recursos disponíveis, dos limites dos trabalhadores e dos limites de sessão. Após a deteção deste problema de desempenho, um campo de descrição do registo de diagnósticos indica se o problema de desempenho está relacionado com os limites de recursos, trabalhadores ou sessões.

Os recursos na Base de Dados Azure SQL são tipicamente referidos aos recursos [DTU](service-tiers-dtu.md) ou [vCore,](service-tiers-vcore.md) e os recursos em Azure SQL Managed Instance são referidos como recursos vCore. O padrão de atingir os limites de recursos é reconhecido quando a degradação do desempenho da consulta detetada é causada por atingir qualquer um dos limites de recursos medidos.

A sessão limita o recurso denota o número de logins simultâneos disponíveis na base de dados. Este padrão de desempenho é reconhecido quando as aplicações ligadas às bases de dados atingiram o número de logins simultâneos disponíveis na base de dados. Se as aplicações tentarem utilizar mais sessões do que disponíveis numa base de dados, o desempenho da consulta é afetado.

Atingir os limites dos trabalhadores é um caso específico de atingir limites de recursos porque os trabalhadores disponíveis não são contados no uso de DTU ou vCore. Atingir os limites dos trabalhadores numa base de dados pode causar o aumento dos tempos de espera específicos dos recursos, o que resulta na degradação do desempenho da consulta.

### <a name="troubleshooting"></a>Resolução de problemas

Os diagnósticos registam saídas de consultas que afetaram as percentagens de desempenho e consumo de recursos. Pode utilizar esta informação como ponto de partida para otimizar a carga de trabalho da sua base de dados. Em particular, pode otimizar as consultas que afetam a degradação do desempenho adicionando índices. Ou pode otimizar aplicações com uma distribuição mais uniforme de carga de trabalho. Se não conseguir reduzir as cargas de trabalho ou fazer otimizações, considere aumentar o nível de preços da subscrição da sua base de dados para aumentar a quantidade de recursos disponíveis.

Se atingiu os limites de sessão disponíveis, pode otimizar as suas aplicações reduzindo o número de logins feitos para a base de dados. Se não conseguir reduzir o número de logins das suas aplicações para a base de dados, considere aumentar o nível de preços da subscrição da sua base de dados. Ou pode dividir e mover a sua base de dados para várias bases de dados para uma distribuição de carga de trabalho mais equilibrada.

Para mais sugestões sobre a resolução dos limites da sessão, consulte [Como lidar com os limites máximos de logins](https://blogs.technet.microsoft.com/latam/20../../how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins/). Consulte a [visão geral dos limites de recursos num servidor](resource-limits-logical-server.md) para obter informações sobre limites nos níveis de servidor e subscrição.

## <a name="workload-increase"></a>Aumento da carga de trabalho

### <a name="what-is-happening"></a>O que é que está a acontecer

Este padrão de desempenho identifica problemas causados por um aumento da carga de trabalho ou, na sua forma mais severa, uma acumulação de carga de trabalho.

Esta deteção é feita através de uma combinação de várias métricas. A métrica básica medida é detetar um aumento da carga de trabalho em comparação com a linha de base de carga de trabalho passada. A outra forma de deteção baseia-se na medição de um grande aumento dos fios ativos do trabalhador que é suficientemente grande para afetar o desempenho da consulta.

Na sua forma mais severa, a carga de trabalho pode acumular-se continuamente devido à incapacidade de uma base de dados para lidar com a carga de trabalho. O resultado é um tamanho de carga de trabalho em crescimento contínuo, que é a condição de acumulação de carga de trabalho. Devido a esta condição, o tempo que a carga de trabalho espera pela execução aumenta. Esta condição representa um dos problemas de desempenho mais graves da base de dados. Esta questão é detetada através da monitorização do aumento do número de fios de trabalhadores abortados.

### <a name="troubleshooting"></a>Resolução de problemas

O registo de diagnósticos produz o número de consultas cuja execução aumentou e o hash de consulta com maior contribuição para o aumento da carga de trabalho. Pode utilizar esta informação como ponto de partida para otimizar a carga de trabalho. A consulta identificada como o maior contribuinte para o aumento da carga de trabalho é especialmente útil como o seu ponto de partida.

Pode considerar distribuir as cargas de trabalho de forma mais homodiosa para a base de dados. Considere otimizar a consulta que está a afetar o desempenho adicionando índices. Também pode distribuir a sua carga de trabalho por várias bases de dados. Se estas soluções não forem possíveis, considere aumentar o nível de preços da subscrição da sua base de dados para aumentar a quantidade de recursos disponíveis.

## <a name="memory-pressure"></a>Pressão da Memória

### <a name="what-is-happening"></a>O que é que está a acontecer

Este padrão de desempenho indica degradação no desempenho atual da base de dados causada pela pressão da memória, ou na sua forma mais severa uma condição de acumulação de memória, em comparação com a linha de desempenho de sete dias passados.

A pressão da memória denota uma condição de desempenho em que há um grande número de fios de trabalhadores que solicitam subsídios de memória. O elevado volume provoca uma elevada condição de utilização da memória em que a base de dados não é capaz de alocar eficientemente a memória a todos os trabalhadores que a solicitem. Uma das razões mais comuns para esta questão está relacionada com a quantidade de memória disponível na base de dados, por um lado. Por outro lado, um aumento da carga de trabalho provoca o aumento dos fios dos trabalhadores e a pressão da memória.

A forma mais severa de pressão de memória é a condição de acumulação de memória. Esta condição indica que um maior número de fios de trabalhadores estão a pedir subsídios de memória do que as consultas que libertam a memória. Este número de fios de trabalhadores que solicitam subsídios de memória também pode estar a aumentar continuamente (acumulando-se) porque o motor de base de dados não consegue alocar a memória de forma eficiente o suficiente para satisfazer a procura. A condição de acumulação de memória representa um dos problemas de desempenho mais graves da base de dados.

### <a name="troubleshooting"></a>Resolução de problemas

O registo de diagnósticos produz os detalhes da loja de objetos de memória com o funcionário (isto é, fio de trabalho) marcado como a razão mais alta para o uso de memória elevada e selos de tempo relevantes. Pode usar esta informação como base para a resolução de problemas.

Pode otimizar ou remover consultas relacionadas com os escriturários com o uso mais elevado da memória. Também pode certificar-se de que não está a consultar dados que não planeia usar. A boa prática é usar sempre uma cláusula WHERE nas suas consultas. Além disso, recomendamos que crie índices não agrupados para procurar os dados em vez de digitalitá-lo.

Também pode reduzir a carga de trabalho otimizando ou distribuindo-a através de várias bases de dados. Ou pode distribuir a sua carga de trabalho por várias bases de dados. Se estas soluções não forem possíveis, considere aumentar o nível de preços da sua base de dados para aumentar a quantidade de recursos de memória disponíveis na base de dados.

Para sugestões adicionais de resolução de problemas, consulte [Memória concede meditação: O misterioso consumidor de memória SQL Server com muitos nomes](https://techcommunity.microsoft.com/t5/sql-server-support/memory-grants-meditation-the-mysterious-sql-server-memory/ba-p/333994).

## <a name="locking"></a>Bloquear

### <a name="what-is-happening"></a>O que é que está a acontecer

Este padrão de desempenho indica uma degradação no desempenho atual da base de dados em que é detetado um bloqueio excessivo de bases de dados em comparação com a linha de base de desempenho dos últimos sete dias.

Nos modernos RDBMS, o bloqueio é essencial para a implementação de sistemas multirfiosos em que o desempenho é maximizado através da execução de múltiplos trabalhadores simultâneos e transações paralelas de bases de dados sempre que possível. O bloqueio neste contexto refere-se ao mecanismo de acesso incorporado no qual apenas uma única transação pode aceder exclusivamente às linhas, páginas, tabelas e ficheiros que são necessários e não competir com outra transação de recursos. Quando a transação que bloqueou os recursos para utilização é feita com eles, o bloqueio desses recursos é liberado, o que permite que outras transações acedam aos recursos necessários. Para obter mais informações sobre o bloqueio, consulte [Lock no motor de base de dados](https://msdn.microsoft.com/library/ms190615.aspx).

Se as transações executadas pelo motor SQL estiverem à espera de períodos de tempo prolongados para aceder a recursos bloqueados para utilização, este tempo de espera provoca o abrandamento do desempenho da execução da carga de trabalho.

### <a name="troubleshooting"></a>Resolução de problemas

Os diagnósticos registam saídas de bloqueio que pode utilizar como base para a resolução de problemas. Pode analisar as consultas de bloqueio relatadas, isto é, as consultas que introduzem a degradação do desempenho do bloqueio, e removê-las. Em alguns casos, você pode ser bem sucedido em otimizar as consultas de bloqueio.

A forma mais simples e segura de mitigar a questão é manter as transações curtas e reduzir a pegada de bloqueio das consultas mais caras. Pode desmantelar um grande lote de operações em operações menores. Boas práticas é reduzir a pegada de bloqueio de consulta, tornando a consulta o mais eficiente possível. Reduza as grandes tomografias porque aumentam as hipóteses de impasse e afetam negativamente o desempenho global da base de dados. Para consultas identificadas que causam o bloqueio, pode criar novos índices ou adicionar colunas ao índice existente para evitar as análises à tabela.

Para mais sugestões, consulte Como resolver problemas de bloqueio que são causados pela escalada de [bloqueio no Servidor SQL](https://support.microsoft.com/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in).

## <a name="increased-maxdop"></a>MAXDOP aumentado

### <a name="what-is-happening"></a>O que é que está a acontecer

Este padrão de desempenho detetável indica uma condição em que um plano de execução de consulta escolhido foi paralelo mais do que deveria ter sido. O optimizador de consultas pode melhorar o desempenho da carga de trabalho executando consultas paralelas para acelerar as coisas sempre que possível. Em alguns casos, os trabalhadores paralelos que processam uma consulta passam mais tempo à espera uns dos outros para sincronizar e fundir resultados em comparação com a execução da mesma consulta com menos trabalhadores paralelos, ou mesmo em alguns casos em comparação com um único fio de trabalhador.

O sistema de peritos analisa o desempenho atual da base de dados em comparação com o período de base. Determina se uma consulta anteriormente em execução está a correr mais lentamente do que antes porque o plano de execução da consulta é mais paralelo do que deveria ser.

A opção de configuração do servidor MAXDOP é usada para controlar quantos núcleos cpu podem ser usados para executar a mesma consulta em paralelo.

### <a name="troubleshooting"></a>Resolução de problemas

Os diagnósticos registam hashes de consulta relacionado com consultas para as quais a duração da execução aumentou porque foram paralelizados mais do que deveriam. O registo também produz tempos de espera CXP. Desta vez representa o tempo em que um único fio de organizador/coordenador (linha 0) está à espera que todos os outros fios terminem antes de fundir os resultados e seguir em frente. Além disso, o registo de diagnósticos produz os tempos de espera que as consultas de mau desempenho aguardavam na execução em geral. Pode usar esta informação como base para a resolução de problemas.

Primeiro, otimize ou simplifique consultas complexas. A boa prática é dividir os longos trabalhos em lotes em pequenos. Além disso, certifique-se de que criou índices para suportar as suas consultas. Também pode impor manualmente o grau máximo de paralelismo (MAXDOP) para uma consulta que foi sinalizada como um mau desempenho. Para configurar esta operação utilizando o T-SQL, consulte [configurar a opção de configuração do servidor MAXDOP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

Configurar a opção de configuração do servidor MAXDOP para zero (0) como um valor predefinido denota que a base de dados pode usar todos os núcleos CPU disponíveis para paralelor fios para executar uma única consulta. A definição de MAXDOP para um (1) denota que apenas um núcleo pode ser utilizado para uma única execução de consulta. Em termos práticos, isto significa que o paralelismo está desligado. Dependendo da base caso-por-caso, dos núcleos disponíveis para a base de dados e da informação de registo de diagnósticos, pode sintonizar a opção MAXDOP ao número de núcleos utilizados para execução de consulta paralela que possam resolver o problema no seu caso.

## <a name="pagelatch-contention"></a>Contenção pagelatch

### <a name="what-is-happening"></a>O que é que está a acontecer

Este padrão de desempenho indica a atual degradação do desempenho da carga de trabalho da base de dados devido à contenção do pagelatch em comparação com a linha de base de carga de trabalho de sete dias passado.

Os fechos são mecanismos de sincronização leves utilizados para permitir a multithreading. Garantem a consistência das estruturas na memória que incluem índices, páginas de dados e outras estruturas internas.

Existem muitos tipos de fechos disponíveis. Para fins de simplicidade, os fechos tampão são usados para proteger páginas de memória na piscina tampão. Os fechos de IO são usados para proteger páginas ainda não carregadas na piscina tampão. Sempre que os dados são escritos ou lidos a partir de uma página no pool tampão, um fio de trabalho precisa adquirir um trinco tampão para a página primeiro. Sempre que um fio de trabalhador tenta aceder a uma página que ainda não está disponível no tampão da memória, é feito um pedido de IO para carregar as informações necessárias a partir do armazenamento. Esta sequência de eventos indica uma forma mais severa de degradação do desempenho.

A contenção nos fechos da página ocorre quando vários fios tentam simultaneamente adquirir fechos na mesma estrutura de memória, o que introduz um tempo de espera acrescido para consultar a execução. No caso da contenção da IO do pagelatch, quando os dados precisam de ser acedidos a partir do armazenamento, este tempo de espera é ainda maior. Pode afetar consideravelmente o desempenho da carga de trabalho. A contenção do Pagelatch é o cenário mais comum de fios à espera uns dos outros e de competir por recursos em vários sistemas de CPU.

### <a name="troubleshooting"></a>Resolução de problemas

Os diagnósticos registam saídas de dados de contenção de pagelatch. Pode usar esta informação como base para a resolução de problemas.

Como um pagelatch é um mecanismo de controlo interno, determina automaticamente quando usá-los. As decisões de aplicação, incluindo o design de esquemas, podem afetar o comportamento do pagelatch devido ao comportamento determinístico dos fechos.

Um método para manusear a contenção do trinco é substituir uma chave de índice sequencial por uma chave não sequencial para distribuir uniformemente inserções através de uma gama de índices. Tipicamente, uma coluna líder no índice distribui a carga de trabalho proporcionalmente. Outro método a considerar é a partilha de mesa. Criar um esquema de partilha de hash com uma coluna computorizada numa mesa dividida é uma abordagem comum para atenuar a contenção excessiva do trinco. No caso da contenção da IO do pagelatch, a introdução de índices ajuda a mitigar esta questão de desempenho.

Para mais informações, consulte [diagnosticar e resolver a contenção do trinco no SQL Server](https://download.microsoft.com/download/B/9/E/B9EDF2CD-1DBF-4954-B81E-82522880A2DC/SQLServerLatchContention.pdf) (download pdf).

## <a name="missing-index"></a>Índice em falta

### <a name="what-is-happening"></a>O que é que está a acontecer

Este padrão de desempenho indica a atual degradação do desempenho da carga de trabalho da base de dados em comparação com a linha de base de sete dias passados devido a um índice em falta.

Um índice é usado para acelerar o desempenho das consultas. Fornece acesso rápido aos dados da tabela, reduzindo o número de páginas de conjuntos de dados que precisam de ser visitadas ou digitalizadas.

Consultas específicas que causaram degradação do desempenho são identificadas através desta deteção para as quais a criação de índices seria benéfica para o desempenho.

### <a name="troubleshooting"></a>Resolução de problemas

Os diagnósticos registam as hashes de consulta para as consultas que foram identificadas para afetar o desempenho da carga de trabalho. Pode construir índices para estas consultas. Também pode otimizar ou remover estas consultas se não forem necessárias. Uma boa prática de desempenho é evitar a consulta de dados que não utiliza.

> [!TIP]
> Sabia que a inteligência incorporada pode gerir automaticamente os índices de melhor desempenho para as suas bases de dados?
>
> Para uma otimização contínua do desempenho, recomendamos que ative a [afinação automática.](automatic-tuning-overview.md) Esta funcionalidade de inteligência incorporada única monitoriza continuamente a sua base de dados e afina automaticamente e cria índices para as suas bases de dados.
>

## <a name="new-query"></a>New Query

### <a name="what-is-happening"></a>O que é que está a acontecer

Este padrão de desempenho indica que é detetada uma nova consulta que está a ter um desempenho fraco e que afeta o desempenho da carga de trabalho em comparação com a linha de base de desempenho de sete dias.

Escrever uma consulta de bom desempenho às vezes pode ser uma tarefa desafiadora. Para mais informações sobre a escrita de consultas, consulte [a Escrita de Consultas SQL](https://msdn.microsoft.com/library/bb264565.aspx). Para otimizar o desempenho da consulta existente, consulte a [afinação da Consulta.](https://msdn.microsoft.com/library/ms176005.aspx)

### <a name="troubleshooting"></a>Resolução de problemas

O registo de diagnósticos produz informações até duas novas consultas mais consumistas de CPU, incluindo as suas hashes de consulta. Como a consulta detetada afeta o desempenho da carga de trabalho, pode otimizar a sua consulta. A boa prática é recuperar apenas os dados que precisa de usar. Recomendamos também a utilização de consultas com uma cláusula WHERE. Recomendamos também que simplifique consultas complexas e as separe em consultas menores. Outra boa prática é decompor grandes consultas de lote em consultas de lote mais pequenas. Introduzir índices para novas consultas é tipicamente uma boa prática para mitigar esta questão de desempenho.

Na Base de Dados Azure SQL, considere utilizar a [Consulta Performance Insight](query-performance-insight-use.md).

## <a name="increased-wait-statistic"></a>Estatística de espera aumentada

### <a name="what-is-happening"></a>O que é que está a acontecer

Este padrão de desempenho detetável indica uma degradação do desempenho da carga de trabalho na qual são identificadas consultas de mau desempenho em comparação com a base de trabalho de sete dias passados.

Neste caso, o sistema não pode classificar as consultas de mau desempenho em qualquer outra categoria de desempenho detetável padrão, mas detetou a estatística de espera responsável pela regressão. Por isso, considera-as como consultas com estatísticas de *espera acrescidas*, onde a estatística de espera responsável pela regressão também está exposta.

### <a name="troubleshooting"></a>Resolução de problemas

Os diagnósticos registam informações sobre o aumento dos detalhes do tempo de espera e as hashes de consulta das consultas afetadas.

Como o sistema não conseguiu identificar com sucesso a causa principal para as consultas de mau desempenho, a informação de diagnóstico é um bom ponto de partida para a resolução manual de problemas. Pode otimizar o desempenho destas consultas. Uma boa prática é recolher apenas dados que precisa de usar e simplificar e quebrar consultas complexas em consultas mais pequenas.

Para mais informações sobre a otimização do desempenho da consulta, consulte a [afinação da Consulta](https://msdn.microsoft.com/library/ms176005.aspx).

## <a name="tempdb-contention"></a>Contenção TempDB

### <a name="what-is-happening"></a>O que é que está a acontecer

Este padrão de desempenho detetável indica uma condição de desempenho da base de dados em que existe um estrangulamento de fios que tentam aceder aos recursos tempDB. (Esta condição não está relacionada com AI.) O cenário típico para esta questão de desempenho são centenas de consultas simultâneas que todos criam, usam e depois largam pequenas tabelas tempDB. O sistema detetou que o número de consultas simultâneas utilizando as mesmas tabelas tempDB aumentou com significado estatístico suficiente para afetar o desempenho da base de dados em comparação com a linha de desempenho dos últimos sete dias.

### <a name="troubleshooting"></a>Resolução de problemas

Os diagnósticos registam saídas tempDB detalhes de contenção. Pode usar a informação como ponto de partida para resolução de problemas. Há duas coisas que pode procurar para aliviar este tipo de contenção e aumentar a entrada da carga de trabalho global: Pode parar de usar as tabelas temporárias. Também pode utilizar tabelas otimizadas pela memória.

Para mais informações, consulte [Introdução a tabelas otimizadas pela memória.](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables)

## <a name="elastic-pool-dtu-shortage"></a>Escassez de DTU da piscina elástica

### <a name="what-is-happening"></a>O que é que está a acontecer

Este padrão de desempenho detetável indica uma degradação no desempenho da carga de trabalho da base de dados atual em comparação com a linha de base de sete dias passado. É devido à escassez de DTUs disponíveis no elástico da sua subscrição.

Os [recursos de piscina elástica azure](elastic-pool-overview.md) são usados como um conjunto de recursos disponíveis partilhados entre várias bases de dados para fins de escala. Quando os recursos eDTU disponíveis na sua piscina elástica não são suficientemente grandes para suportar todas as bases de dados da piscina, um problema de desempenho de escassez de DTU de piscina elástica é detetado pelo sistema.

### <a name="troubleshooting"></a>Resolução de problemas

O registo de diagnósticos fornece informações sobre o pool elástico, lista as principais bases de dados de dtu-consumir, e fornece uma percentagem do DTU da piscina usado pela base de dados mais consumista.

Como esta condição de desempenho está relacionada com várias bases de dados usando o mesmo conjunto de eDTUs na piscina elástica, os passos de resolução de problemas concentram-se nas bases de dados de topo que consomem DTU. Pode reduzir a carga de trabalho nas bases de dados mais consumistas, o que inclui a otimização das consultas mais consumistas nessas bases de dados. Também pode garantir que não está a consultar dados que não utiliza. Outra abordagem é otimizar as aplicações utilizando as principais bases de dados de DTU e redistribuir a carga de trabalho entre várias bases de dados.

Se não for possível a redução e otimização da carga de trabalho atual nas suas principais bases de dados de consumo de DTU, considere aumentar o seu nível de preços de piscina elástica. Este aumento resulta no aumento das DTUs disponíveis na piscina elástica.

## <a name="plan-regression"></a>Plano regressão

### <a name="what-is-happening"></a>O que é que está a acontecer

Este padrão de desempenho detetável denota uma condição em que a base de dados utiliza um plano de execução de consulta subóptima. O plano sub-ideal normalmente causa um aumento da execução de consultas, o que leva a tempos de espera mais longos para as consultas atuais e outras.

O motor de base de dados determina o plano de execução da consulta com o menor custo para uma execução de consulta. À medida que o tipo de consultas e cargas de trabalho mudam, às vezes os planos existentes já não são eficientes, ou talvez o motor da base de dados não tenha feito uma boa avaliação. Por uma questão de correção, os planos de execução de consulta podem ser forçados manualmente.

Este padrão de desempenho detetável combina três casos diferentes de regressão do plano: nova regressão do plano, regressão do plano antigo, e os planos existentes mudaram a carga de trabalho. O tipo particular de regressão do plano que ocorreu é fornecido na propriedade de *detalhes* no registo de diagnósticos.

A nova condição de regressão do plano refere-se a um estado em que o motor de base de dados começa a executar um novo plano de execução de consultas que não é tão eficiente como o plano antigo. A antiga condição de regressão do plano refere-se ao estado quando o motor da base de dados muda de usar um novo plano mais eficiente para o plano antigo, que não é tão eficiente como o novo plano. Os planos existentes alteraram a regressão da carga de trabalho refere-se ao estado em que os antigos e os novos planos alternam continuamente, com o equilíbrio a ir mais para o plano de baixo desempenho.

Para mais informações sobre regressões de planos, veja o que é a regressão do [plano no Servidor SQL?](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../what-is-plan-regression-in-sql-server/)

### <a name="troubleshooting"></a>Resolução de problemas

Os diagnósticos registam as hashes de consulta, identificação de bom plano, identificação de mau plano e identificação de consulta. Pode usar esta informação como base para a resolução de problemas.

Pode analisar qual o plano que melhor se apresenta para as suas consultas específicas que pode identificar com as hashes de consulta fornecidas. Depois de determinar qual o plano que funciona melhor para as suas consultas, pode forçá-lo manualmente.

Para mais informações, consulte [como o SQL Server impede regressões](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions/)de planos .

> [!TIP]
> Sabia que a funcionalidade de inteligência incorporada pode gerir automaticamente os planos de execução de consultas mais bem executados para as suas bases de dados?
>
> Para uma otimização contínua do desempenho, recomendamos que ative a [afinação automática.](automatic-tuning-overview.md) Esta funcionalidade de inteligência incorporada monitoriza continuamente a sua base de dados e afina automaticamente e cria planos de execução de consultas de melhor desempenho para as suas bases de dados.

## <a name="database-scoped-configuration-value-change"></a>Alteração do valor da configuração com código de base de dados

### <a name="what-is-happening"></a>O que é que está a acontecer

Este padrão de desempenho detetável indica uma condição em que uma alteração na configuração de base de dados provoca a regressão do desempenho que é detetada em comparação com o comportamento de carga de trabalho da base de dados de sete dias passado. Este padrão denota que uma alteração recente feita na configuração de base de dados não parece ser benéfica para o desempenho da sua base de dados.

As alterações de configuração com um espaço de dados podem ser definidas para cada base de dados individual. Esta configuração é utilizada caso a caso para otimizar o desempenho individual da sua base de dados. As seguintes opções podem ser configuradas para cada base de dados individual: MAXDOP, LEGACY_CARDINALITY_ESTIMATION, PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES e CLEAR PROCEDURE_CACHE.

### <a name="troubleshooting"></a>Resolução de problemas

As alterações de configuração de log de diagnóstico que foram feitas recentemente que causaram degradação do desempenho em comparação com o comportamento de carga de trabalho de sete dias anteriores. Pode reverter as alterações de configuração para os valores anteriores. Também pode afinar valor por valor até atingir o nível de desempenho desejado. Pode copiar valores de configuração de âmbito de dados a partir de uma base de dados semelhante com desempenho satisfatório. Se não conseguir perturbar o desempenho, volte aos valores predefinidos e tente afinar a partir desta linha de base.

Para obter mais informações sobre a otimização da configuração com o código de base de dados e a sintaxe T-SQL sobre a alteração da configuração, consulte a configuração com [o alcance da base de dados Alter (Transact-SQL)](https://msdn.microsoft.com/library/mt629158.aspx).

## <a name="slow-client"></a>Cliente Lento

### <a name="what-is-happening"></a>O que é que está a acontecer

Este padrão de desempenho detetável indica uma condição em que o cliente que utiliza a base de dados não pode consumir a saída da base de dados tão rapidamente quanto a base de dados envia os resultados. Como a base de dados não está a armazenar os resultados das consultas executadas num tampão, abranda e espera que o cliente consuma as saídas de consulta transmitidas antes de prosseguir. Esta condição também pode estar relacionada com uma rede que não é suficientemente rápida o suficiente para transmitir saídas da base de dados ao cliente consumista.

Esta condição só é gerada se for detetada uma regressão de desempenho em comparação com o comportamento da carga de trabalho da base de dados de sete dias. Este problema de desempenho só é detetado se ocorrer uma degradação de desempenho estatisticamente significativa em comparação com o comportamento de desempenho anterior.

### <a name="troubleshooting"></a>Resolução de problemas

Este padrão de desempenho detetável indica uma condição do lado do cliente. É necessária uma resolução de problemas na aplicação do lado do cliente ou na rede do lado do cliente. O registo de diagnósticos produz as hashes de consulta e os tempos de espera que parecem estar mais à espera que o cliente os consuma nas últimas duas horas. Pode usar esta informação como base para a resolução de problemas.

Pode otimizar o desempenho da sua aplicação para consumo destas consultas. Também pode considerar possíveis problemas de latência da rede. Como o problema da degradação do desempenho foi baseado na mudança na base de desempenho dos últimos sete dias, você pode investigar se as recentes alterações de aplicação ou condição de rede causaram este evento de regressão de desempenho.

## <a name="pricing-tier-downgrade"></a>Desvalorização do nível de preços

### <a name="what-is-happening"></a>O que é que está a acontecer

Este padrão de desempenho detetável indica uma condição em que o nível de preços da subscrição da sua base de dados foi desvalorizado. Devido à redução dos recursos (DTUs) disponíveis na base de dados, o sistema detetou uma queda no desempenho atual da base de dados em comparação com a linha de base dos últimos sete dias.

Além disso, pode haver uma condição em que o nível de preços da subscrição da sua base de dados foi desclassificado e depois atualizado para um nível mais elevado num curto espaço de tempo. A deteção desta degradação temporária do desempenho é saída na secção de detalhes do registo de diagnósticos como uma desvalorização e atualização do nível de preços.

### <a name="troubleshooting"></a>Resolução de problemas

Se reduziste o teu nível de preços, e portanto as DTUs disponíveis, e estás satisfeito com o desempenho, não há nada que precises de fazer. Se reduzir o seu nível de preços e não estiver satisfeito com o desempenho da sua base de dados, reduza as cargas de trabalho da sua base de dados ou considere aumentar o nível de preços para um nível mais elevado.

## <a name="recommended-troubleshooting-flow"></a>Fluxo recomendado de resolução de problemas

 Siga o fluxograma para obter uma abordagem recomendada para problemas de desempenho utilizando Insights Inteligentes.

Aceda a Smart Insights através do portal Azure, indo para a Azure SQL Analytics. Tente localizar o alerta de desempenho e selecione-o. Identifique o que está a acontecer na página de deteções. Observe a análise de causa seleção fornecida do problema, texto de consulta, tendências de tempo de consulta e evolução do incidente. Tente resolver o problema utilizando a recomendação Da Insights Inteligentes para atenuar a questão do desempenho.

[![Gráfico de fluxo de resolução de problemas](./media/intelligent-insights-troubleshoot-performance/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> Selecione o fluxograma para descarregar uma versão PDF.

Insights Inteligentes geralmente precisam de uma hora de tempo para realizar a análise da causa raiz do problema de desempenho. Se não conseguir localizar o seu problema em Insights Inteligentes e é fundamental para si, utilize a Loja de Consultas para identificar manualmente a causa principal do problema de desempenho. (Normalmente, estas questões têm menos de uma hora.) Para mais informações, consulte [o desempenho do Monitor utilizando a Loja de Consultas](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).

## <a name="next-steps"></a>Próximos passos

- Aprenda conceitos [de Insights Inteligentes.](intelligent-insights-overview.md)
- Utilize o registo de [diagnósticos](intelligent-insights-use-diagnostics-log.md)de desempenho de Insights Inteligentes .
- Monitor utilizando [o Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql).
- Aprenda a [recolher e consumir dados de registo dos seus recursos Azure.](../../azure-monitor/platform/platform-logs-overview.md)
