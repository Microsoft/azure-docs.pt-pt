---
title: Tipos de problemas de desempenho de consulta na Base de Dados Azure SQL
description: Neste artigo, conheça os tipos de problemas de desempenho de consulta na Base de Dados Azure SQL e aprenda também a identificar e resolver consultas com estas questões
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: troubleshooting
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: wiassaf, sstein
ms.date: 03/10/2020
ms.openlocfilehash: 6ea17f04538e3444b1baddaa8862add2cfbbaa9c
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493428"
---
# <a name="detectable-types-of-query-performance-bottlenecks-in-azure-sql-database"></a>Tipos detetáveis de estrangulamento de desempenho de consultas na Base de Dados SQL do Azure
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Ao tentar resolver um estrangulamento de desempenho, comece por determinar se o estrangulamento está a ocorrer enquanto a consulta se encontra em estado de funcionamento ou em estado de espera. Aplicam-se diferentes resoluções em função desta determinação. Utilize o diagrama seguinte para ajudar a compreender os fatores que podem causar um problema relacionado com a execução ou um problema relacionado com a espera. Neste artigo são discutidos problemas e resoluções relativos a cada tipo de problema.

Pode utilizar insights [inteligentes](database/intelligent-insights-troubleshoot-performance.md#detectable-database-performance-patterns) de base de dados Azure SQL ou [DMVs](database/monitoring-with-dmvs.md) de servidor SQL para detetar este tipo de estrangulamentos de desempenho.

![Estados de carga de trabalho](./media/identify-query-performance-issues/workload-states.png)

**Problemas relacionados com a execução**: Os problemas relacionados com a execução estão geralmente relacionados com problemas de compilação que resultam num plano de consulta sub-ideais ou problemas de execução relacionados com recursos insuficientes ou sobreusados.
**Problemas relacionados com** a espera : Os problemas relacionados com a espera estão geralmente relacionados com:

- Fechaduras (bloqueio)
- I/O
- Contenção relacionada com o uso do TempDB
- Bolsa de memória espera

## <a name="compilation-problems-resulting-in-a-suboptimal-query-plan"></a>Problemas de compilação que resultam num plano de consulta sub-ideais

Um plano sub-otimizador de consultas pode ser a causa de um desempenho de consulta lenta. O SQL Query Optimizer pode produzir um plano sub-otimizador devido a um índice em falta, estatísticas velhas, uma estimativa incorreta do número de linhas a processar, ou uma estimativa imprecisa da memória necessária. Se sabe que a consulta foi executada mais rapidamente no passado ou em outro caso, compare os planos de execução reais para ver se são diferentes.

- Identificar quaisquer índices em falta utilizando um destes métodos:

  - Utilizar [insights inteligentes.](database/intelligent-insights-troubleshoot-performance.md#missing-index)
  - [Base de dados Advisor](database/database-advisor-implement-performance-recommendations.md) para bases de dados individuais e agrizadas.
  - DMVs. Este exemplo mostra-lhe o impacto de um índice em falta, como detetar um [índice em falta](database/performance-guidance.md#identifying-and-adding-missing-indexes) usando DMVs, e o impacto da implementação da recomendação de índice em falta.
- Tente aplicar [dicas de consulta,](/sql/t-sql/queries/hints-transact-sql-query) [atualizar estatísticas](/sql/t-sql/statements/update-statistics-transact-sql)ou [reconstruir índices](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes) para obter o melhor plano. Permitir [a correção automática do plano](../azure-sql/database/automatic-tuning-overview.md) na Base de Dados Azure SQL para atenuar automaticamente estes problemas.

  Este [exemplo](database/performance-guidance.md#query-tuning-and-hinting) mostra o impacto de um plano de consulta sub-ideais devido a uma consulta parametrizada, como detetar esta condição, e como usar uma dica de consulta para resolver.

- Tente alterar o nível de compatibilidade da base de dados e implementar o processamento de consultas inteligentes. O SQL Query Optimizer pode gerar um plano de consulta diferente dependendo do nível de compatibilidade da sua base de dados. Níveis de compatibilidade mais elevados proporcionam [capacidades de processamento de consulta](/sql/relational-databases/performance/intelligent-query-processing)mais inteligentes.

  - Para obter mais informações sobre o processamento de consultas, consulte [o Guia de Arquitetura de Processamento de Consultas.](/sql/relational-databases/query-processing-architecture-guide)
  - Para alterar os níveis de compatibilidade da base de dados e ler mais sobre as diferenças entre os níveis de compatibilidade, consulte [a BASE DE DADOS ALTER](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).
  - Para ler mais sobre a estimativa do cardinalício, consulte [a Estimativa cardinalício](/sql/relational-databases/performance/cardinality-estimation-sql-server)

## <a name="resolving-queries-with-suboptimal-query-execution-plans"></a>Resolução de consultas com planos de execução de consultas sub-ideais

As secções seguintes discutem como resolver consultas com plano de execução de consultas sub-ideais.

### <a name="queries-that-have-parameter-sensitive-plan-psp-problems"></a><a name="ParamSniffing"></a> Consultas que têm problemas de plano sensível a parâmetros (PSP)

Um problema de plano sensível a parâmetros (PSP) acontece quando o otimizador de consulta gera um plano de execução de consulta que é ideal apenas para um valor específico de parâmetro (ou conjunto de valores) e o plano em cache não é então ideal para valores de parâmetros que são usados em execuções consecutivas. Os planos que não são os melhores podem então causar problemas de desempenho de consulta e degradar a carga de trabalho global.

Para obter mais informações sobre o processamento de erros de parâmetros e consulta, consulte o [guia de arquitetura de processamento de consultas.](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing)

Várias soluções alternativas podem atenuar problemas da PSP. Cada solução tem trocas e inconvenientes associados:

- Utilize a indicação de consulta [RECOMPILE](/sql/t-sql/queries/hints-transact-sql-query) em cada execução de consulta. Esta solução de soluções comercia o tempo de compilação e o aumento da CPU para uma melhor qualidade do plano. A `RECOMPILE` opção muitas vezes não é possível para cargas de trabalho que requerem uma produção elevada.
- Utilize a sugestão de consulta [OPTION (OTIMIZE FOR...)](/sql/t-sql/queries/hints-transact-sql-query) para anular o valor do parâmetro real com um valor de parâmetro típico que produz um plano suficientemente bom para a maioria das possibilidades de valor dos parâmetros. Esta opção requer uma boa compreensão dos valores ótimos dos parâmetros e das características do plano associados.
- Utilize a sugestão de consulta [OPÇÃO (OTIMIZE FOR UNKNOWN)](/sql/t-sql/queries/hints-transact-sql-query) para anular o valor real do parâmetro e, em vez disso, utilizar a média do vetor de densidade. Também pode fazê-lo capturando os valores dos parâmetros de entrada em variáveis locais e, em seguida, usando as variáveis locais dentro dos predicados em vez de usar os próprios parâmetros. Para esta correção, a densidade média deve ser *suficientemente boa*.
- Desative o cheiro do parâmetro inteiramente utilizando a sugestão de consulta [DISABLE_PARAMETER_SNIFFING.](/sql/t-sql/queries/hints-transact-sql-query)
- Utilize a sugestão de consulta [KEEPFIXEDPLAN](/sql/t-sql/queries/hints-transact-sql-query) para evitar recompilações em cache. Esta solução pressupõe que o plano comum já é o que está em cache. Também pode desativar atualizações de estatísticas automáticas para reduzir as hipóteses de o bom plano ser despejado e um novo plano mau será compilado.
- Force o plano utilizando explicitamente a sugestão [de plano de UTILIZAÇÃO](/sql/t-sql/queries/hints-transact-sql-query) reescrevendo a consulta e adicionando a sugestão no texto da consulta. Ou defina um plano específico utilizando a Loja de Consultas ou permitindo a [sintonização automática](../azure-sql/database/automatic-tuning-overview.md).
- Substitua o procedimento único por um conjunto aninhado de procedimentos que podem ser utilizados com base na lógica condicional e nos valores dos parâmetros associados.
- Crie alternativas dinâmicas de execução de cordas a uma definição de procedimento estático.

Para obter mais informações sobre a resolução de problemas da PSP, consulte estas publicações de blog:

- [Sinto o cheiro de um parâmetro.](/archive/blogs/queryoptteam/i-smell-a-parameter)
- [Conor vs. SQL dinâmico vs. procedimentos vs. qualidade do plano para consultas parametrizadas](/archive/blogs/conor_cunningham_msft/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries)
- [Técnicas de otimização de consultas SQL no SQL Server: Farejar parâmetros](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>Compilar atividade causada por parametrização inadequada

Quando uma consulta tem literais, ou o motor de base de dados parametiza automaticamente a declaração ou um utilizador explicitamente parametriza a declaração para reduzir o número de compilações. Um elevado número de compilações para uma consulta usando o mesmo padrão, mas diferentes valores literais podem resultar em alta utilização do CPU. Da mesma forma, se apenas parcialmente parametrizar uma consulta que continua a ter literais, o motor da base de dados não parametriza ainda mais a consulta.

Aqui está um exemplo de uma consulta parcialmente parametrizada:

```sql
SELECT *
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

Neste exemplo, `t1.c1` `@p1` toma, mas `t2.c2` continua a tomar GUID como literal. Neste caso, se alterar o valor `c2` para, a consulta é tratada como uma consulta diferente, e uma nova compilação acontecerá. Para reduzir compilações neste exemplo, também parametrizaria o GUID.

A seguinte consulta mostra a contagem de consultas por haxixe de consulta para determinar se uma consulta é adequadamente parametrizada:

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

### <a name="factors-that-affect-query-plan-changes"></a>Fatores que afetam as alterações do plano de consulta

Uma compensação do plano de execução de consultas pode resultar num plano de consulta gerado que difere do plano em cache original. Um plano original existente pode ser automaticamente recompiliado por várias razões:

- As alterações no esquema são referenciadas pela consulta
- As alterações de dados nas tabelas são referenciadas pela consulta
- As opções de contexto de consulta foram alteradas

Um plano compilado pode ser ejetado da cache por várias razões, tais como:

- Caso reinicia
- Alterações de configuração com âmbito de dados
- Pressão da memória
- Pedidos explícitos para limpar a cache

Se usar uma sugestão RECOMPILE, um plano não será em cache.

Uma recompilação (ou compilação fresca após o despejo de cache) ainda pode resultar na geração de um plano de execução de consultas idêntico ao original. Quando o plano muda do plano anterior ou original, estas explicações são prováveis:

- **Alterado design físico**: Por exemplo, os índices recém-criados cobrem mais eficazmente os requisitos de uma consulta. Os novos índices podem ser usados numa nova compilação se o otimizador de consulta decidir que usar esse novo índice é mais ideal do que usar a estrutura de dados que foi originalmente selecionada para a primeira versão da execução de consultas. Quaisquer alterações físicas nos objetos referenciados podem resultar numa nova escolha do plano no momento da compilação.

- **Diferenças de recursos** do servidor : Quando um plano de um sistema difere do plano noutro sistema, a disponibilidade de recursos, como o número de processadores disponíveis, pode influenciar o plano gerado. Por exemplo, se um sistema tiver mais processadores, poderá ser escolhido um plano paralelo.

- **Estatísticas diferentes**: As estatísticas associadas aos objetos referenciados podem ter mudado ou podem ser materialmente diferentes das estatísticas do sistema original. Se as estatísticas mudarem e uma recompilação acontecer, o otimizador de consulta utiliza as estatísticas a partir do momento em que mudaram. As distribuições e frequências de dados revistas das estatísticas podem diferir das da compilação original. Estas mudanças são usadas para criar estimativas de cardinalidade. (As *estimativas da Cardinalidade* são o número de linhas que se espera que fluam através da árvore de consulta lógica.) Alterações nas estimativas de cardinaldade podem levá-lo a escolher diferentes operadores físicos e ordens de operações associadas. Mesmo pequenas alterações nas estatísticas podem resultar numa alteração do plano de execução de consultas.

- Alterar o **nível de compatibilidade da base de dados ou a versão do estimador cardinalício**: As alterações ao nível de compatibilidade da base de dados podem permitir novas estratégias e funcionalidades que possam resultar num plano de execução de consultas diferente. Para além do nível de compatibilidade da base de dados, um bloco de rastreios desativado ou ativado 4199 ou um estado alterado da configuração de âmbito de base de dados QUERY_OPTIMIZER_HOTFIXES também podem influenciar as escolhas do plano de execução de consultas no momento de compilação. As bandeiras de rastreio 9481 (legado de força CE) e 2312 (padrão de força CE) também afetam o plano.

## <a name="resource-limits-issues"></a>Questões de limites de recursos

O desempenho lento da consulta não relacionado com planos de consulta sub-ideais e índices em falta estão geralmente relacionados com recursos insuficientes ou sobreusados. Se o plano de consulta for o ideal, a consulta (e a base de dados) pode estar a atingir os limites de recursos para a base de dados, piscina elástica ou instância gerida. Um exemplo pode ser o excesso de registo escrever produção para o nível de serviço.

- Detetar problemas de recursos utilizando o portal Azure: Para ver se os limites de recursos são o problema, consulte [a monitorização dos recursos da Base de Dados SQL](database/monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring). Para bases de dados individuais e piscinas elásticas, consulte [recomendações de desempenho do Database Advisor](database/database-advisor-implement-performance-recommendations.md) e Insights de Desempenho de [Consulta](database/query-performance-insight-use.md).
- Deteção de limites de recursos utilizando [insights inteligentes](database/intelligent-insights-troubleshoot-performance.md#reaching-resource-limits)
- Deteção de problemas de recursos [utilizando DMVs:](database/monitoring-with-dmvs.md)

  - O [DMV sys.dm_db_resource_stats](database/monitoring-with-dmvs.md#monitor-resource-use) devolve CPU, I/O e consumo de memória para a base de dados. Existe uma linha para cada intervalo de 15 segundos, mesmo que não haja atividade na base de dados. Os dados históricos são mantidos por uma hora.
  - O [DMV sys.resource_stats](database/monitoring-with-dmvs.md#monitor-resource-use) devolve os dados de utilização e armazenamento do CPU para a Base de Dados Azure SQL. Os dados são recolhidos e agregados em intervalos de cinco minutos.
  - [Muitas consultas individuais que consomem cumulativamente alta CPU](database/monitoring-with-dmvs.md#many-individual-queries-that-cumulatively-consume-high-cpu)

Se identificar o problema como recurso insuficiente, pode atualizar recursos para aumentar a capacidade da sua base de dados para absorver os requisitos do CPU. Para obter mais informações, consulte [os recursos de base de dados únicos em Azure SQL Database](database/single-database-scale.md) e [dimensionar recursos de piscina elástica na Base de Dados Azure SQL](database/elastic-pool-scale.md). Para obter informações sobre a escala de um caso gerido, consulte [os limites de recursos de nível de serviço](managed-instance/resource-limits.md#service-tier-characteristics)

## <a name="performance-problems-caused-by-increased-workload-volume"></a>Problemas de desempenho causados pelo aumento do volume de trabalho

Um aumento do tráfego de aplicações e do volume de carga de trabalho pode causar um aumento da utilização do CPU. Mas deve ter cuidado para diagnosticar corretamente este problema. Quando vir um problema de alta CPU, responda a estas questões para determinar se o aumento é causado por alterações no volume de carga de trabalho:

- As consultas da aplicação são a causa do problema de alta CPU?
- Para as [principais consultas que consomem CPU que pode identificar:](database/monitoring-with-dmvs.md#the-cpu-issue-occurred-in-the-past)

  - Vários planos de execução estavam associados à mesma consulta? Se sim, porquê?
  - Para consultas com o mesmo plano de execução, os tempos de execução foram consistentes? A contagem de execução aumentou? Em caso afirmativo, o aumento da carga de trabalho está provavelmente a causar problemas de desempenho.

Em resumo, se o plano de execução de consulta não executou de forma diferente, mas o uso do CPU aumentou juntamente com a contagem de execução, o problema de desempenho está provavelmente relacionado com um aumento de carga de trabalho.

Nem sempre é fácil identificar uma mudança de volume de trabalho que está a conduzir um problema de CPU. Considere estes fatores:

- **Alteração da utilização do recurso**: Por exemplo, considere um cenário em que o uso do CPU aumentou para 80% durante um longo período de tempo. Só o uso do CPU não significa que o volume de carga de trabalho tenha mudado. As regressões no plano de execução de consultas e as alterações na distribuição de dados também podem contribuir para uma maior utilização dos recursos, mesmo que a aplicação execute a mesma carga de trabalho.

- **O aparecimento de uma nova consulta:** Uma aplicação pode conduzir um novo conjunto de consultas em diferentes momentos.

- **Aumento ou diminuição do número de pedidos**: Este cenário é a medida mais óbvia de uma carga de trabalho. O número de consultas nem sempre corresponde a uma utilização mais de recursos. No entanto, esta métrica ainda é um sinal significativo, assumindo que outros fatores são inalterados.

Utilize insights inteligentes para detetar [aumentos de carga de trabalho](database/intelligent-insights-troubleshoot-performance.md#workload-increase) e planear [regressões](database/intelligent-insights-troubleshoot-performance.md#plan-regression).

## <a name="waiting-related-problems"></a>Problemas relacionados com a espera

Uma vez eliminado um plano subópto e problemas *relacionados com a espera* que estão relacionados com problemas de execução, o problema de desempenho geralmente é que as consultas estão provavelmente à espera de algum recurso. Problemas relacionados com a espera podem ser causados por:

- **Bloqueio:**

  Uma consulta pode manter o bloqueio em objetos na base de dados enquanto outros tentam aceder aos mesmos objetos. Pode identificar consultas de bloqueio utilizando [DMVs](database/monitoring-with-dmvs.md#monitoring-blocked-queries) ou [Insights Inteligentes](database/intelligent-insights-troubleshoot-performance.md#locking).
- **Problemas de IO**

  As consultas podem estar à espera que as páginas sejam escritas para os dados ou ficheiros de registo. Neste caso, verifique as `INSTANCE_LOG_RATE_GOVERNOR` estatísticas , ou espere no `WRITE_LOG` `PAGEIOLATCH_*` DMV. Consulte a utilização de DMVs para [identificar problemas de desempenho de IO](database/monitoring-with-dmvs.md#identify-io-performance-issues).
- **Problemas temporários**

  Se a carga de trabalho utilizar tabelas temporárias ou se houver derrames temporários nos planos, as consultas podem ter um problema com a produção tempDB. Consulte a utilização de DMVs para [identificar problemas tempDB](database/monitoring-with-dmvs.md#identify-tempdb-performance-issues).
- **Problemas relacionados com a memória**

  Se a carga de trabalho não tiver memória suficiente, a esperança de vida da página pode cair, ou as consultas podem ter menos memória do que precisam. Em alguns casos, a inteligência incorporada no Otimizador de Consulta irá corrigir problemas relacionados com a memória. Consulte a utilização de DMVs para [identificar problemas de concessão de memória](database/monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues).

### <a name="methods-to-show-top-wait-categories"></a>Métodos para mostrar as principais categorias de espera

Estes métodos são comumente utilizados para mostrar as principais categorias de tipos de espera:

- Use Insights Inteligentes para identificar consultas com degradação do desempenho devido ao [aumento das esperas](database/intelligent-insights-troubleshoot-performance.md#increased-wait-statistic)
- Utilize [a Loja de Consultas](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) para encontrar estatísticas de espera para cada consulta ao longo do tempo. Na Loja de Consultas, os tipos de espera são combinados em categorias de espera. Pode encontrar o mapeamento das categorias de espera para os tipos de espera em [sys.query_store_wait_stats](/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- Utilize [sys.dm_db_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) para devolver informações sobre todas as esperas encontradas por fios executados durante uma operação de consulta. Pode utilizar esta vista agregada para diagnosticar problemas de desempenho com a Base de Dados Azure SQL e também com consultas e lotes específicos. As consultas podem estar à espera de recursos, esperas de filas ou esperas externas.
- Utilize [sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) para devolver informações sobre a fila de tarefas que aguardam algum recurso.

Em cenários de alto CPU, as estatísticas de Loja de Consultas e de espera podem não refletir a utilização do CPU se:

- Consultas de alto consumo de CPU ainda estão a ser executadas.
- As consultas de alta mente que consomem CPU estavam a decorrer quando aconteceu uma falha.

Os DMVs que acompanham a Query Store e as estatísticas de espera mostram resultados para consultas concluídas e cronometradas com sucesso. Não mostram dados para a execução de declarações até que as declarações terminem. Utilize a visão dinâmica da gestão [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) para acompanhar as consultas atualmente executadas e o tempo de trabalho associado.

> [!TIP]
> Ferramentas adicionais:
>
> - [TigerToolbox espera e trava](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [Usp_whatsup TigerToolbox](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="next-steps"></a>Passos seguintes

[Monitorização e afinação da base de dados SQL](database/monitor-tune-overview.md)