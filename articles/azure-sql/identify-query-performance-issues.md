---
title: Tipos de problemas de desempenho de consulta na Base de Dados Azure SQL
description: Neste artigo, conheça os tipos de problemas de desempenho da consulta na Base de Dados Azure SQL e também aprenda a identificar e resolver consultas com estas questões
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: edf22c7f1086c1d13717adaff514c42aab3a4cfd
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84046923"
---
# <a name="detectable-types-of-query-performance-bottlenecks-in-azure-sql-database"></a>Tipos detetáveis de estrangulamentos de desempenho de consulta na Base de Dados Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Ao tentar resolver um estrangulamento de desempenho, comece por determinar se o estrangulamento está a ocorrer enquanto a consulta está em estado de execução ou em estado de espera. As diferentes resoluções aplicam-se em função desta determinação. Utilize o diagrama seguinte para ajudar a compreender os fatores que podem causar um problema relacionado com o funcionamento ou um problema relacionado com a espera. Neste artigo são discutidos problemas e resoluções relativos a cada tipo de problema.

Pode utilizar [insights inteligentes](database/intelligent-insights-troubleshoot-performance.md#detectable-database-performance-patterns) de base de dados Azure SQL ou [DMVs](database/monitoring-with-dmvs.md) do Servidor SQL para detetar este tipo de estrangulamentos de desempenho.

![Estados de carga de trabalho](./media/identify-query-performance-issues/workload-states.png)

**Problemas relacionados com**o funcionamento : Os problemas relacionados com o funcionamento estão geralmente relacionados com problemas de compilação que resultam num plano de consulta sub-óptimo ou problemas de execução relacionados com recursos insuficientes ou sobreutilizados.
**Problemas relacionados com**a espera : Os problemas relacionados com a espera estão geralmente relacionados com:

- Fechaduras (bloqueio)
- I/O
- Contenção relacionada com a utilização do TempDB
- Subsídio de memória espera

## <a name="compilation-problems-resulting-in-a-suboptimal-query-plan"></a>Problemas de compilação que resultam num plano de consulta sub-ideal

Um plano sub-ideal gerado pelo SQL Query Optimizer pode ser a causa de um desempenho lento de consulta. O SQL Query Optimizer pode produzir um plano sub-ideal devido a um índice em falta, estatísticas velhas, uma estimativa incorreta do número de linhas a serem processadas, ou uma estimativa imprecisa da memória necessária. Se sabe que a consulta foi executada mais rapidamente no passado ou em outro caso, compare os planos de execução reais para ver se são diferentes.

- Identifique quaisquer índices em falta utilizando um destes métodos:

  - Use [Insights Inteligentes.](database/intelligent-insights-troubleshoot-performance.md#missing-index)
  - [Base de Dados Advisor](database/database-advisor-implement-performance-recommendations.md) para bases de dados individuais e reunidas.
  - DMVs. Este exemplo mostra-lhe o impacto de um índice em falta, como detetar um [índice em falta](database/performance-guidance.md#identifying-and-adding-missing-indexes) usando DMVs, e o impacto da implementação da recomendação do índice em falta.
- Tente aplicar dicas de [consulta,](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) [estatísticas de atualização](https://docs.microsoft.com/sql/t-sql/statements/update-statistics-transact-sql)ou [reconstruir índices](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes) para obter o melhor plano. Ative a [correção automática](../azure-sql/database/automatic-tuning-overview.md) do plano na Base de Dados Azure SQL para mitigar automaticamente estes problemas.

  Este [exemplo](database/performance-guidance.md#query-tuning-and-hinting) mostra o impacto de um plano de consulta sub-ideal devido a uma consulta parametrizada, como detetar esta condição, e como usar uma dica de consulta para resolver.

- Tente alterar o nível de compatibilidade da base de dados e implementar o processamento inteligente de consultas. O Optimizer de Consulta SQL pode gerar um plano de consulta diferente dependendo do nível de compatibilidade para a sua base de dados. Níveis de compatibilidade mais elevados fornecem capacidades de processamento de consultas mais [inteligentes.](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing)

  - Para mais informações sobre o processamento de consultas, consulte o Guia de Arquitetura de Processamento de [Consultas.](https://docs.microsoft.com/sql/relational-databases/query-processing-architecture-guide)
  - Para alterar os níveis de compatibilidade da base de dados e ler mais sobre as diferenças entre os níveis de compatibilidade, consulte alter [DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).
  - Para ler mais sobre a estimativa de cardinalidade, consulte [estimativa de Cardinalidade](https://docs.microsoft.com/sql/relational-databases/performance/cardinality-estimation-sql-server)

## <a name="resolving-queries-with-suboptimal-query-execution-plans"></a>Resolução de consultas com planos de execução de consulta subóptimas

As seguintes secções discutem como resolver consultas com plano de execução de consulta subideal.

### <a name="queries-that-have-parameter-sensitive-plan-psp-problems"></a><a name="ParamSniffing"></a>Consultas que têm problemas com plano sensível de parâmetros (PSP)

Um problema de plano sensível parametro (PSP) acontece quando o optimizador de consultas gera um plano de execução de consulta que é ideal apenas para um valor de parâmetro específico (ou conjunto de valores) e o plano cached não é então ideal para valores de parâmetros que são usados em execuções consecutivas. Planos que não são ideais podem então causar problemas de desempenho de consulta e degradar a carga de trabalho geral.

Para obter mais informações sobre o cheiro dos parâmetros e o processamento de consultas, consulte o guia de [arquitetura de processamento de consultas.](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing)

Várias sobras podem atenuar problemas da PSP. Cada suposições tem trocas e inconvenientes associados:

- Utilize a dica de consulta [RECOMPILE](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) em cada execução de consulta. Esta suposição de suposição comercializa o tempo de compilação e o aumento da CPU para uma melhor qualidade do plano. A `RECOMPILE` opção muitas vezes não é possível para cargas de trabalho que requerem uma alta entrada.
- Use a [opção (OTIMIZE FOR...)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) dica para sobrepor o valor real do parâmetro com um valor de parâmetro típico que produz um plano que é bom o suficiente para a maioria das possibilidades de valor do parâmetro. Esta opção requer uma boa compreensão dos valores ideais dos parâmetros e das características do plano associados.
- Use a dica de [opção (OTIMIZE FOR UNKNOWN)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) para sobrepor ao valor real do parâmetro e, em vez disso, use a média do vetor de densidade. Também pode fazê-lo capturando os valores dos parâmetros de entrada em variáveis locais e, em seguida, usando as variáveis locais dentro dos predicados em vez de usar os próprios parâmetros. Para esta correção, a densidade média deve ser *suficientemente boa.*
- Desative o snifador do parâmetro utilizando a sugestão [de consulta DISABLE_PARAMETER_SNIFFING.](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)
- Utilize a sugestão de consulta [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) para evitar recompilações em cache. Esta suposição pressupõe que o plano comum já está em cache. Também pode desativar atualizações estatísticas automáticas para reduzir as hipóteses de o bom plano ser despejado e ser elaborado um novo plano mau.
- Force o plano usando explicitamente a dica de consulta DO [PLANO DE UTILIZAÇÃO](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) reescrevendo a consulta e adicionando a dica no texto de consulta. Ou defina um plano específico utilizando a Consulta Store ou permitindo a [afinação automática](../azure-sql/database/automatic-tuning-overview.md).
- Substitua o procedimento único por um conjunto de procedimentos aninhados que cada um pode ser utilizado com base na lógica condicional e nos valores dos parâmetros associados.
- Criar alternativas dinâmicas de execução de cordas a uma definição de procedimento estático.

Para obter mais informações sobre a resolução de problemas da PSP, consulte estas publicações de blogue:

- [Sinto o cheiro de um parâmetro.](https://docs.microsoft.com/archive/blogs/queryoptteam/i-smell-a-parameter)
- [Conor vs. SQL dinâmico vs. procedimentos vs. qualidade do plano para consultas parametrizadas](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)
- [Técnicas de otimização de consulta SQL no Servidor SQL: Cheirar parâmetros](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>Compilação de atividade causada por parametrização inadequada

Quando uma consulta tem literal, ou o motor de base de dados parametia automaticamente a declaração ou um utilizador parametriza explicitamente a declaração para reduzir o número de compilações. Um elevado número de compilações para uma consulta usando o mesmo padrão, mas diferentes valores literais podem resultar em alta utilização de CPU. Da mesma forma, se apenas parametrizar parcialmente uma consulta que continua a ter literais, o motor de base de dados não parametia ainda mais a consulta.

Aqui está um exemplo de uma consulta parcialmente parametrizada:

```sql
SELECT *
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

Neste exemplo, `t1.c1` `@p1` toma, mas `t2.c2` continua a tomar o GUID como literal. Neste caso, se alterar o valor para `c2` , a consulta é tratada como uma consulta diferente, e uma nova compilação acontecerá. Para reduzir as compilações neste exemplo, também parametria o GUID.

A seguinte consulta mostra a contagem de consultas por hash consulta para determinar se uma consulta é adequadamente parametrizada:

```sql
SELECT TOP 10
  q.query_hash
  , count (distinct p.query_id ) AS number_of_distinct_query_ids
  , min(qt.query_sql_text) AS sampled_query_text
FROM sys.query_store_query_text AS qt
  JOIN sys.query_store_query AS q
     ON qt.query_text_id = q.query_text_id
  JOIN sys.query_store_plan AS p
     ON q.query_id = p.query_id
  JOIN sys.query_store_runtime_stats AS rs
     ON rs.plan_id = p.plan_id
  JOIN sys.query_store_runtime_stats_interval AS rsi
     ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
WHERE
  rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
  AND query_parameterization_type_desc IN ('User', 'None')
GROUP BY q.query_hash
ORDER BY count (distinct p.query_id) DESC
```

### <a name="factors-that-affect-query-plan-changes"></a>Fatores que afetam alterações no plano de consulta

Uma recompilação do plano de execução de consulta pode resultar num plano de consulta gerado que difere do plano original de cached. Um plano original existente pode ser automaticamente recompilado por várias razões:

- As alterações no esquema são referenciadas pela consulta
- As alterações de dados nas tabelas são referenciadas pela consulta
- As opções de contexto de consulta foram alteradas

Um plano compilado pode ser ejetado da cache por várias razões, tais como:

- Instância reinicia
- Alterações de configuração com código de base de dados
- Pressão da memória
- Pedidos explícitos para limpar a cache

Se utilizar uma sugestão de RECOMPILE, um plano não será em cache.

Uma recompilação (ou compilação fresca após o despejo de cache) ainda pode resultar na geração de um plano de execução de consulta que é idêntico ao original. Quando o plano muda do plano anterior ou original, estas explicações são prováveis:

- **Design físico alterado**: Por exemplo, os índices recém-criados cobrem mais eficazmente os requisitos de uma consulta. Os novos índices podem ser usados numa nova compilação se o optimizador de consultas decidir que usar esse novo índice é mais ideal do que usar a estrutura de dados que foi originalmente selecionada para a primeira versão da execução da consulta. Quaisquer alterações físicas nos objetos referenciados podem resultar numa nova escolha de plano no momento da compilação.

- **Diferenças de recursos**do servidor : Quando um plano num sistema difere do plano noutro sistema, a disponibilidade de recursos, como o número de processadores disponíveis, pode influenciar o plano gerado. Por exemplo, se um sistema tiver mais processadores, poderá ser escolhido um plano paralelo.

- **Estatísticas diferentes**: As estatísticas associadas aos objetos referenciados podem ter mudado ou podem ser materialmente diferentes das estatísticas do sistema original. Se as estatísticas mudarem e uma recompilação acontecer, o optimizador de consultas utiliza as estatísticas a partir do momento em que mudaram. As distribuições e frequências de dados revistas das estatísticas podem diferir das da compilação original. Estas mudanças são usadas para criar estimativas de cardinalidade. (As estimativas de*Cardinalidade* são o número de linhas que se espera que fluam através da árvore de consulta lógica.) As alterações às estimativas de cardinalidade podem levá-lo a escolher diferentes operadores físicos e ordens de operações associadas. Mesmo pequenas alterações nas estatísticas podem resultar numa alteração no plano de execução da consulta.

- **Alteração do nível**de compatibilidade da base de dados ou versão do estimador de cardinalidade : Alterações ao nível de compatibilidade da base de dados podem permitir novas estratégias e funcionalidades que possam resultar num plano de execução de consultas diferente. Para além do nível de compatibilidade da base de dados, uma bandeira de rastreio desativada ou ativada 4199 ou um estado alterado da configuração com aplicação de base de dados QUERY_OPTIMIZER_HOTFIXES também podem influenciar as escolhas do plano de execução de consultas no momento da compilação. As bandeiras de vestígios 9481 (legado de força CE) e 2312 (força padrão CE) também afetam o plano.

## <a name="resource-limits-issues"></a>Problemas de limites de recursos

O desempenho lento da consulta não relacionado com planos de consulta sub-ideais e índices em falta estão geralmente relacionados com recursos insuficientes ou sobreutilizados. Se o plano de consulta for o ideal, a consulta (e a base de dados) podem estar a atingir os limites de recursos para a base de dados, o pool elástico ou a instância gerida. Um exemplo pode ser o excesso de entrada de log write para o nível de serviço.

- Detetar problemas de recursos utilizando o portal Azure: Para ver se os limites de recursos são o problema, consulte a monitorização dos recursos da Base de [Dados SQL](database/monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring). Para bases de dados individuais e piscinas elásticas, consulte [recomendações](database/database-advisor-implement-performance-recommendations.md) de desempenho do Database Advisor Advisor e [Questionry Performance Insights](database/query-performance-insight-use.md).
- Deteção de limites de recursos utilizando [Insights Inteligentes](database/intelligent-insights-troubleshoot-performance.md#reaching-resource-limits)
- Deteção de problemas de recursos utilizando [DMVs:](database/monitoring-with-dmvs.md)

  - O [DMV sys.dm_db_resource_stats](database/monitoring-with-dmvs.md#monitor-resource-use) devolve CPU, I/O e consumo de memória para uma base de dados SQL. Existe uma linha para cada intervalo de 15 segundos, mesmo que não haja atividade na base de dados. Os dados históricos são mantidos durante uma hora.
  - O [DMV sys.resource_stats](database/monitoring-with-dmvs.md#monitor-resource-use) devolve dados de utilização e armazenamento de CPU para a Base de Dados Azure SQL. Os dados são recolhidos e agregados em intervalos de cinco minutos.
  - [Muitas consultas individuais que consomem cumulativamente alta CPU](database/monitoring-with-dmvs.md#many-individual-queries-that-cumulatively-consume-high-cpu)

Se identificar o problema como recurso insuficiente, pode atualizar recursos para aumentar a capacidade da sua base de dados SQL para absorver os requisitos do CPU. Para mais informações, consulte os recursos de base de dados individuais da Scale na Base de [Dados Azure SQL](database/single-database-scale.md) e na Escala de recursos de piscina elástica na Base de [Dados Azure SQL](database/elastic-pool-scale.md). Para obter informações sobre a escala de uma instância gerida, consulte [os limites de recursos de nível de serviço](managed-instance/resource-limits.md#service-tier-characteristics)

## <a name="performance-problems-caused-by-increased-workload-volume"></a>Problemas de desempenho causados pelo aumento do volume de trabalho

Um aumento do tráfego de aplicações e do volume de carga de trabalho pode causar um aumento da utilização do CPU. Mas deve ter cuidado para diagnosticar corretamente este problema. Quando vir um problema de alta CPU, responda a estas questões para determinar se o aumento é causado por alterações no volume de carga de trabalho:

- As perguntas da aplicação são a causa do problema da Alta CPU?
- Para as [principais consultas que consomem CPU que você pode identificar:](database/monitoring-with-dmvs.md#the-cpu-issue-occurred-in-the-past)

  - Vários planos de execução foram associados à mesma consulta? Se sim, porquê?
  - Para consultas com o mesmo plano de execução, os tempos de execução foram consistentes? A contagem de execução aumentou? Em caso afirmativo, o aumento da carga de trabalho está provavelmente a causar problemas de desempenho.

Em resumo, se o plano de execução da consulta não executou de forma diferente, mas o uso de CPU aumentou juntamente com a contagem de execução, o problema de desempenho está provavelmente relacionado com um aumento da carga de trabalho.

Nem sempre é fácil identificar uma mudança de volume de carga que está a conduzir um problema de CPU. Considere estes fatores:

- **Alteração da utilização**dos recursos : Por exemplo, considere um cenário em que o uso de CPU aumentou para 80 por cento durante um longo período de tempo. O uso de CPU por si só não significa que o volume de carga de trabalho mudou. As regressões no plano de execução da consulta e as alterações na distribuição de dados também podem contribuir para uma maior utilização de recursos, mesmo que a aplicação execute a mesma carga de trabalho.

- **O aparecimento de uma nova consulta**: Uma aplicação pode conduzir um novo conjunto de consultas em diferentes momentos.

- **Aumento ou diminuição do número de pedidos**: Este cenário é a medida mais óbvia de uma carga de trabalho. O número de consultas nem sempre corresponde a mais utilização de recursos. No entanto, esta métrica ainda é um sinal significativo, assumindo que outros fatores se mantêm inalterados.

Utilize insights inteligentes para detetar [aumentos](database/intelligent-insights-troubleshoot-performance.md#workload-increase) de carga de trabalho e [regressões de planos.](database/intelligent-insights-troubleshoot-performance.md#plan-regression)

## <a name="waiting-related-problems"></a>Problemas relacionados com a espera

Depois de ter eliminado um plano sub-óptimo e problemas *relacionados com* a espera relacionados com problemas de execução, o problema de desempenho é que, em geral, as consultas estão provavelmente à espera de algum recurso. Problemas relacionados com a espera podem ser causados por:

- **Bloqueio:**

  Uma consulta pode manter o bloqueio em objetos na base de dados enquanto outros tentam aceder aos mesmos objetos. Pode identificar consultas de bloqueio utilizando [DMVs](database/monitoring-with-dmvs.md#monitoring-blocked-queries) ou [Insights Inteligentes](database/intelligent-insights-troubleshoot-performance.md#locking).
- **Problemas de IO**

  As consultas podem estar à espera que as páginas sejam escritas nos dados ou ficheiros de registo. Neste caso, verifique as estatísticas de `INSTANCE_LOG_RATE_GOVERNOR` , `WRITE_LOG` ou `PAGEIOLATCH_*` aguardar no DMV. Consulte a utilização de DMVs para identificar problemas de [desempenho da IO](database/monitoring-with-dmvs.md#identify-io-performance-issues).
- **Problemas tempDB**

  Se a carga de trabalho utilizar tabelas temporárias ou houver derrames tempDB nos planos, as consultas podem ter um problema com a entrada tempDB. Consulte o uso de DMVs para [identificar problemas tempDB](database/monitoring-with-dmvs.md#identify-tempdb-performance-issues).
- **Problemas relacionados com a memória**

  Se a carga de trabalho não tiver memória suficiente, a esperança de vida da página pode diminuir, ou as consultas podem ter menos memória do que precisam. Em alguns casos, a inteligência incorporada no Optimizer de Consulta resolverá problemas relacionados com a memória. Consulte a utilização de DMVs para [identificar problemas](database/monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)de concessão de memória .

### <a name="methods-to-show-top-wait-categories"></a>Métodos para mostrar as principais categorias de espera

Estes métodos são comumente utilizados para mostrar as categorias de topo dos tipos de espera:

- Use Insights Inteligentes para identificar consultas com degradação do desempenho devido ao [aumento das esperas](database/intelligent-insights-troubleshoot-performance.md#increased-wait-statistic)
- Utilize a [Consulta Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) para encontrar estatísticas de espera para cada consulta ao longo do tempo. Na Loja de Consultas, os tipos de espera são combinados em categorias de espera. Você pode encontrar o mapeamento de categorias de espera para esperar tipos em [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- Utilize [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) para devolver informações sobre todas as esperas encontradas por fios que foram executados durante uma operação de consulta. Pode utilizar esta vista agregada para diagnosticar problemas de desempenho com base de dados Azure SQL e também com consultas e lotes específicos. As consultas podem estar à espera de recursos, esperas de fila ou esperas externas.
- Utilize [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) para devolver informações sobre a fila de tarefas que estão à espera de algum recurso.

Em cenários de alta CPU, a Consulta Store e as estatísticas de espera podem não refletir o uso do CPU se:

- As consultas de alta consumista da CPU ainda estão a ser executadas.
- As altas consultas consumistas estavam a decorrer quando aconteceu uma falha.

DMVs que rastreiam A Query Store e as estatísticas de espera mostram resultados apenas com sucesso e consultas cronometradas. Não mostram dados para executar declarações até que as declarações terminem. Utilize a visão dinâmica de gestão [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) para acompanhar as consultas atualmente executadas e o tempo de trabalho associado.

> [!TIP]
> Ferramentas adicionais:
>
> - [TigerToolbox espera e trava](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [usp_whatsup tigerToolbox](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="next-steps"></a>Próximos passos

[Visão geral da base de dados SQL](database/monitor-tune-overview.md)
