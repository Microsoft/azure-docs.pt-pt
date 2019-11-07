---
title: Monitoramento e ajuste de desempenho – banco de dados SQL do Azure
description: Dicas para ajuste de desempenho no banco de dados SQL do Azure por meio de avaliação e aperfeiçoamento.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 01/25/2019
ms.openlocfilehash: c11112963ec82a0e53df156048495e7b5141bcb7
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687761"
---
# <a name="monitoring-and-performance-tuning"></a>Ajuste de monitorização e desempenho

O banco de dados SQL do Azure fornece ferramentas e métodos que você pode usar para monitorar o uso facilmente, adicionar ou remover recursos (como CPU, memória ou e/s), solucionar problemas potenciais e fazer recomendações para melhorar o desempenho de um banco de dados. Os recursos no banco de dados SQL do Azure podem corrigir automaticamente os problemas nos bancos dos dados. 

O ajuste automático permite que um banco de dados se adapte à carga de trabalho e otimize automaticamente o desempenho. No entanto, alguns problemas personalizados podem precisar de solução de problemas. Este artigo explica algumas práticas recomendadas e algumas ferramentas que você pode usar para solucionar problemas de desempenho.

Para garantir que um banco de dados seja executado sem problemas, você deve:
- [Monitore o desempenho do banco de dados](#monitor-database-performance) para garantir que os recursos atribuídos ao banco de dados possam lidar com a carga de trabalho. Se o banco de dados estiver atingindo os limites de recurso, considere:
   - Identificar e otimizar as principais consultas que consomem recursos.
   - Adicionar mais recursos [atualizando a camada de serviço](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources).
- [Solucionar problemas de desempenho](#troubleshoot-performance-problems) para identificar por que um problema potencial ocorreu e identificar a causa raiz do problema. Depois de identificar a causa raiz, execute as etapas para corrigir o problema.

## <a name="monitor-database-performance"></a>Monitorizar o desempenho da base de dados

Para monitorar o desempenho de um banco de dados SQL no Azure, comece monitorando os recursos usados em relação ao nível de desempenho do banco de dados escolhido. Monitore os seguintes recursos:
 - **Uso da CPU**: Verifique se o banco de dados está atingindo 100% do uso da CPU por um longo período de tempo. O alto uso da CPU pode indicar que você precisa identificar e ajustar as consultas que usam a capacidade de computação mais alta. O alto uso da CPU também pode indicar que o banco de dados ou a instância deve ser atualizada para uma camada de serviço superior. 
 - **Estatísticas de espera**: use [Sys. dm_os_wait_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) para determinar por quanto tempo as consultas estão aguardando. As consultas podem estar aguardando recursos, esperas de fila ou esperas externas. 
 - **Uso de e/s**: Verifique se o banco de dados está atingindo os limites de e/s do armazenamento subjacente.
 - **Uso de memória**: a quantidade de memória disponível para o banco de dados ou instância é proporcional ao número de vCores. Verifique se a memória é suficiente para a carga de trabalho. A expectativa de vida da página é um dos parâmetros que podem indicar a rapidez com que as páginas são removidas da memória.

O serviço de banco de dados SQL do Azure inclui ferramentas e recursos para ajudá-lo a solucionar problemas de desempenho potenciais. Você pode identificar oportunidades para melhorar e otimizar o desempenho da consulta sem alterar os recursos, revisando as [recomendações de ajuste de desempenho](sql-database-advisor.md). 

Os índices em falta e as consultas mal otimizadas são razões comuns para um desempenho fraco da base de dados. Você pode aplicar recomendações de ajuste para melhorar o desempenho da carga de trabalho. Você também pode permitir que o banco de dados SQL do Azure [otimize automaticamente o desempenho das consultas](sql-database-automatic-tuning.md) aplicando todas as recomendações identificadas. Em seguida, verifique se as recomendações melhoraram o desempenho do banco de dados.

> [!NOTE]
> A indexação está disponível apenas no banco de dados individual e em pools elásticos. A indexação não está disponível em uma instância gerenciada.

Escolha entre as seguintes opções para monitorar e solucionar problemas de desempenho do banco de dados:

- No [portal do Azure](https://portal.azure.com), selecione bancos de dados **SQL** e selecione o Database. No gráfico de **monitoramento** , procure recursos que se aproximam da utilização máxima. O consumo de DTU é mostrado por padrão. Selecione **Editar** para alterar o intervalo de tempo e os valores mostrados.
- Ferramentas como SQL Server Management Studio fornecem muitos relatórios úteis, como o [painel de desempenho](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard). Use esses relatórios para monitorar o uso de recursos e identificar as principais consultas de consumo de recursos. Você pode usar [repositório de consultas](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed) para identificar consultas cujo desempenho foi regressivo.
- No [portal do Azure](https://portal.azure.com), use [análise de desempenho de consultas](sql-database-query-performance.md) para identificar as consultas que usam a maioria dos recursos. Esse recurso está disponível somente em um banco de dados individual e em pools elásticos.
- Use [Assistente do banco de dados SQL](sql-database-advisor-portal.md) para exibir recomendações para ajudá-lo a criar e remover índices, parametrizar consultas e corrigir problemas de esquema. Esse recurso está disponível somente em um banco de dados individual e em pools elásticos.
- Use o [Azure SQL Intelligent insights](sql-database-intelligent-insights.md) para monitorar automaticamente o desempenho do banco de dados. Quando um problema de desempenho é detectado, um log de diagnóstico é gerado. O log fornece detalhes e uma RCA (análise de causa raiz) do problema. Uma recomendação de melhoria de desempenho é fornecida quando possível.
- [Habilite o ajuste automático](sql-database-automatic-tuning-enable.md) para permitir que o banco de dados SQL do Azure corrija problemas de desempenho automaticamente.
- Use [DMVs (exibições de gerenciamento dinâmico)](sql-database-monitoring-with-dmvs.md), [eventos estendidos](sql-database-xevent-db-diff-from-svr.md)e [repositório de consultas](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) para obter ajuda com a solução de problemas de desempenho mais detalhada.

> [!TIP]
> Depois de identificar um problema de desempenho, confira nossas [diretrizes de desempenho](sql-database-performance-guidance.md) para encontrar técnicas para melhorar o desempenho do banco de dados SQL do Azure.

## <a name="troubleshoot-performance-problems"></a>Solucionar problemas de desempenho

Para diagnosticar e resolver problemas de desempenho, comece descobrindo o estado de cada consulta ativa e as condições que causam problemas de desempenho relevantes para cada Estado de carga de trabalho. Para melhorar o desempenho do banco de dados SQL do Azure, você precisa entender que cada solicitação de consulta ativa do aplicativo está em estado de execução ou em espera. Ao solucionar um problema de desempenho no banco de dados SQL do Azure, tenha em mente o diagrama a seguir.

![Estados de carga de trabalho](./media/sql-database-monitor-tune-overview/workload-states.png)

Um problema de desempenho em uma carga de trabalho pode ser causado pela contenção de CPU (uma condição *relacionada à execução* ) ou por consultas individuais que estão aguardando algo (uma condição *relacionada à espera* ).

Problemas relacionados à execução podem ser causados por:
- **Problemas de compilação**: o otimizador de consulta do SQL pode produzir um plano de qualidade inferior devido a estatísticas obsoletas, uma estimativa incorreta do número de linhas a serem processadas ou uma estimativa imprecisa da memória necessária. Se você souber que a consulta foi executada mais rapidamente no passado ou em outra instância (uma instância gerenciada ou uma instância de SQL Server), compare os planos de execução reais para ver se eles são diferentes. Tente aplicar dicas de consulta ou recompilar estatísticas ou índices para obter um plano melhor. Habilite a correção automática de plano no banco de dados SQL do Azure para atenuar esses problemas automaticamente.
- **Problemas de execução**: se o plano de consulta for ideal, é provável que ele esteja atingindo os limites de recursos do banco de dados, como a taxa de transferência de gravação de log. Ou pode estar usando índices fragmentados que devem ser recriados. Problemas de execução também podem ocorrer quando um grande número de consultas simultâneas precisa dos mesmos recursos. Problemas *relacionados à espera* geralmente estão relacionados a problemas de execução, pois as consultas que não são executadas com eficiência provavelmente estão aguardando alguns recursos.

Problemas relacionados à espera podem ser causados por:
- **Bloqueio**: uma consulta pode manter o bloqueio em objetos no banco de dados enquanto outros tentam acessar os mesmos objetos. Você pode identificar consultas de bloqueio usando DMVs ou ferramentas de monitoramento.
- **Problemas de e/s**: as consultas podem estar aguardando que as páginas sejam gravadas nos arquivos de dados ou de log. Nesse caso, verifique as estatísticas de espera `INSTANCE_LOG_RATE_GOVERNOR`, `WRITE_LOG`ou `PAGEIOLATCH_*` no DMV.
- **Problemas de tempdb**: se a carga de trabalho usa tabelas temporárias ou há derramamentos de tempdb nos planos, as consultas podem ter um problema com a taxa de transferência de tempdb. 
- **Problemas relacionados à memória**: se a carga de trabalho não tiver memória suficiente, a expectativa de vida da página poderá ser descartada ou as consultas poderão ter menos memória do que precisam. Em alguns casos, a inteligência interna no otimizador de consulta corrigirá os problemas relacionados à memória.
 
As seções a seguir explicam como identificar e solucionar alguns tipos de problemas.

## <a name="performance-problems-related-to-running"></a>Problemas de desempenho relacionados à execução do

Como uma diretriz geral, se o uso da CPU for consistente ou acima de 80 por cento, seu problema de desempenho estará relacionado à execução. Um problema relacionado à execução pode ser causado por recursos de CPU insuficientes. Ou pode estar relacionado a uma das seguintes condições:

- Muitas consultas em execução
- Muitas consultas de compilação
- Uma ou mais consultas em execução que usam um plano de consulta de qualidade inferior

Se você encontrar um problema de desempenho relacionado à execução, seu objetivo é identificar o problema preciso usando um ou mais métodos. Esses métodos são as maneiras mais comuns de identificar problemas relacionados à execução:

- Use o [portal do Azure](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal) para monitorar o uso percentual de CPU.
- Use as [DMVs](sql-database-monitoring-with-dmvs.md)a seguir:

  - A DMV [Sys. dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) retorna CPU, e/s e consumo de memória para um banco de dados SQL. Existe uma linha para cada intervalo de 15 segundos, mesmo que não haja atividade no banco de dados. Os dados históricos são mantidos por uma hora.
  - A DMV [Sys. resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) retorna o uso de CPU e dados de armazenamento para o banco de dado SQL do Azure. Os dados são coletados e agregados em intervalos de cinco minutos.

> [!IMPORTANT]
> Para solucionar problemas de uso de CPU para consultas T-SQL que usam DMVs sys. dm_db_resource_stats e sys. resource_stats, consulte [identificar problemas de desempenho da CPU](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues).

### <a name="ParamSniffing"></a>Consultas que têm problemas de PSP

Um problema de PSP (plano sensível ao parâmetro) ocorre quando o otimizador de consulta gera um plano de execução de consulta que é ideal apenas para um valor de parâmetro específico (ou conjunto de valores) e o plano em cache não é ideal para os valores de parâmetro que são usados em consecutivas execuções. Os planos que não são ideais podem causar problemas de desempenho de consulta e degradar a taxa de transferência geral da carga de trabalho. 

Para obter mais informações sobre detecção de parâmetros e processamento de consultas, consulte o [Guia de arquitetura de processamento de consultas](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

Várias soluções alternativas podem reduzir os problemas de PSP. Cada solução alternativa tem compensações e desvantagens associadas:

- Use a dica de consulta [recompile](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) em cada execução de consulta. Essa solução alternativa compensa o tempo de compilação e a CPU aumentada para melhor qualidade do plano. A opção de `RECOMPILE` geralmente não é possível para cargas de trabalho que exigem alta taxa de transferência.
- Use a dica de consulta [Option (Optimize for...)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) para substituir o valor do parâmetro real por um valor de parâmetro típico que produz um plano que é bom o suficiente para a maioria das possibilidades de valor de parâmetro. Essa opção requer uma boa compreensão dos valores de parâmetro ideais e das características do plano associado.
- Use a dica de consulta [opção (otimizar para desconhecido)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) para substituir o valor real do parâmetro e, em vez disso, use a média do vetor de densidade. Você também pode fazer isso capturando os valores de parâmetro de entrada em variáveis locais e, em seguida, usando as variáveis locais dentro dos predicados em vez de usar os próprios parâmetros. Para essa correção, a densidade média deve ser *boa o suficiente*.
- Desabilite a detecção de parâmetros inteiramente usando a dica de consulta [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) .
- Use a dica de consulta [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) para evitar recompilações no cache. Essa solução alternativa pressupõe que o plano comum bom o suficiente é o já existente no cache. Você também pode desabilitar as atualizações automáticas de estatísticas para reduzir as chances de que o bom plano seja removido e um novo plano incorreto será compilado.
- Force o plano usando explicitamente a dica de consulta [use Plan](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) , reescrevendo a consulta e adicionando a dica no texto da consulta. Ou defina um plano específico usando Repositório de Consultas ou habilitando o [ajuste automático](sql-database-automatic-tuning.md).
- Substitua o único procedimento por um conjunto aninhado de procedimentos que podem ser usados com base em lógica condicional e os valores de parâmetro associados.
- Crie alternativas de execução de cadeia de caracteres dinâmica para uma definição de procedimento estático.

Para obter mais informações sobre como resolver problemas de PSP, consulte estas Postagens de blog:

- [Eu Smell um parâmetro](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/)
- [Conor vs. SQL vs. procedimentos vs. qualidade do plano para consultas parametrizadas](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)
- [Técnicas de otimização de consulta SQL no SQL Server: detecção de parâmetros](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>Atividade de compilação causada por parametrização incorreta

Quando uma consulta tem literais, o mecanismo de banco de dados automaticamente parametriza a instrução ou um usuário parametriza explicitamente a instrução para reduzir o número de compilações. Um alto número de compilações para uma consulta usando o mesmo padrão, mas valores literais diferentes podem resultar em alto uso da CPU. Da mesma forma, se você apenas parcialmente parametrizar uma consulta que continua a ter literais, o mecanismo de banco de dados não parametrizará mais a consulta.  

Aqui está um exemplo de uma consulta com parâmetros parcialmente:

```sql
SELECT * 
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

Neste exemplo, `t1.c1` usa `@p1`, mas `t2.c2` continua a obter o GUID como literal. Nesse caso, se você alterar o valor de `c2`, a consulta será tratada como uma consulta diferente e uma nova compilação ocorrerá. Para reduzir as compilações neste exemplo, você também parametrizaria o GUID.

A consulta a seguir mostra a contagem de consultas por hash de consulta para determinar se uma consulta está parametrizada corretamente:

```sql
SELECT  TOP 10  
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

Uma recompilação do plano de execução de consulta pode resultar em um plano de consulta gerado diferente do plano original em cache. Um plano original existente pode ser recompilado automaticamente por vários motivos:
- As alterações no esquema são referenciadas pela consulta.
- As alterações de dados nas tabelas são referenciadas pela consulta. 
- As opções de contexto de consulta foram alteradas.

Um plano compilado pode ser ejetado do cache por vários motivos, como:

- A instância é reiniciada.
- Alterações de configuração no escopo do banco de dados.
- Pressão de memória.
- Solicitações explícitas para limpar o cache.

Se você usar uma dica de recompilação, um plano não será armazenado em cache.

Uma recompilação (ou uma nova compilação após a remoção do cache) ainda pode resultar na geração de um plano de execução de consulta que seja idêntico ao original. Quando o plano muda do plano anterior ou original, é provável que essas explicações sejam:

- **Design físico alterado**: por exemplo, índices recém-criados abordam com mais eficiência os requisitos de uma consulta. Os novos índices podem ser usados em uma nova compilação se o otimizador de consulta decidir que usar esse novo índice é mais adequado do que usar a estrutura de dados que foi originalmente selecionada para a primeira versão da execução da consulta.  Qualquer alteração física nos objetos referenciados pode resultar em uma nova opção de plano no momento da compilação.

- **Diferenças de recursos de servidor**: quando um plano em um sistema difere do plano em outro sistema, a disponibilidade de recursos, como o número de processadores disponíveis, pode influenciar qual plano é gerado.  Por exemplo, se um sistema tiver mais processadores, um plano paralelo poderá ser escolhido. 

- **Estatísticas diferentes**: as estatísticas associadas aos objetos referenciados podem ter sido alteradas ou podem ser materiais diferentes das estatísticas do sistema original.  Se as estatísticas forem alteradas e uma recompilação ocorrer, o otimizador de consulta usará as estatísticas a partir de quando elas forem alteradas. As frequências e as distribuições de dados das estatísticas revisadas podem ser diferentes das da compilação original.  Essas alterações são usadas para criar estimativas de cardinalidade. (*Estimativas de cardinalidade* são o número de linhas que devem fluir por meio da árvore de consulta lógica.) As alterações nas estimativas de cardinalidade podem levá-lo a escolher diferentes operadores físicos e ordens associadas de operações.  Até mesmo alterações secundárias em estatísticas podem resultar em um plano de execução de consulta alterado.

- **Nível de compatibilidade do banco de dados alterado ou versão do avaliador de cardinalidade**: alterações no nível de compatibilidade do banco de dados podem habilitar novas estratégias e recursos que podem resultar em um plano de execução de consulta diferente.  Além do nível de compatibilidade do banco de dados, um sinalizador de rastreamento desabilitado ou habilitado 4199 ou um estado alterado da configuração no escopo do banco de dados QUERY_OPTIMIZER_HOTFIXES também pode influenciar as opções do plano de execução de consulta no momento da compilação.  Os sinalizadores de rastreamento 9481 (forçar herança CE) e 2312 (forçar padrão CE) também afetam o plano. 

### <a name="resolve-problem-queries-or-provide-more-resources"></a>Resolver consultas de problemas ou fornecer mais recursos

Depois de identificar o problema, você pode ajustar as consultas de problema ou atualizar o tamanho da computação ou a camada de serviço para aumentar a capacidade do banco de dados SQL para absorver os requisitos de CPU. 

Para obter mais informações, consulte [dimensionar recursos de banco de dados individual no banco de dados SQL do Azure](sql-database-single-database-scale.md) e [dimensionar recursos de pool elástico no banco de dados SQL do Azure](sql-database-elastic-pool-scale.md). Para obter informações sobre como dimensionar uma instância gerenciada, consulte [limites de recursos da camada de serviço](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

### <a name="performance-problems-caused-by-increased-workload-volume"></a>Problemas de desempenho causados pelo aumento do volume de carga de trabalho

Um aumento no tráfego do aplicativo e no volume da carga de trabalho pode causar um aumento no uso da CPU. Mas você deve ter cuidado para diagnosticar corretamente esse problema. Quando você vir um problema de alta CPU, responda a essas perguntas para determinar se o aumento é causado por alterações no volume de carga de trabalho:

- As consultas do aplicativo são a causa do problema de alta CPU?
- Para obter as principais consultas que consomem a CPU que você pode identificar:

   - Foram associados vários planos de execução à mesma consulta? Em caso afirmativo, por quê?
   - Para consultas com o mesmo plano de execução, os tempos de execução são consistentes? A contagem de execuções aumentou? Nesse caso, o aumento da carga de trabalho provavelmente está causando problemas de desempenho.

Em resumo, se o plano de execução de consulta não foi executado de forma diferente, mas o uso da CPU aumentou junto com a contagem de execução, o problema de desempenho provavelmente está relacionado a um aumento de carga de trabalho.

Nem sempre é fácil identificar uma alteração de volume de carga de trabalho que está gerando um problema de CPU. Considere estes fatores: 

- **Uso de recursos alterado**: por exemplo, considere um cenário em que o uso da CPU aumentou para 80% por um longo período de tempo.  O uso da CPU sozinho não significa que o volume da carga de trabalho foi alterado. As regressões no plano de execução de consulta e as alterações na distribuição de dados também podem contribuir para mais utilização de recursos, embora o aplicativo execute a mesma carga de trabalho.

- **A aparência de uma nova consulta**: um aplicativo pode direcionar um novo conjunto de consultas em momentos diferentes.

- **Um aumento ou uma diminuição no número de solicitações**: esse cenário é a medida mais óbvia de uma carga de trabalho. O número de consultas nem sempre corresponde a mais utilização de recursos. No entanto, essa métrica ainda é um sinal significativo, supondo que outros fatores não sejam alterados.

## <a name="waiting-related-performance-problems"></a>Problemas de desempenho relacionados à espera 

Se você tiver certeza de que seu problema de desempenho não está relacionado ao alto uso da CPU ou à execução, seu problema está relacionado à espera. Ou seja, os recursos de CPU não estão sendo usados com eficiência porque a CPU está aguardando algum outro recurso. Nesse caso, identifique o que os recursos de CPU estão aguardando. 

Esses métodos são comumente usados para mostrar as principais categorias de tipos de espera:

- Use [repositório de consultas](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) para localizar estatísticas de espera para cada consulta ao longo do tempo. Em Repositório de Consultas, os tipos de espera são combinados em categorias de espera. Você pode encontrar o mapeamento de categorias de espera para esperar tipos em [Sys. query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- Use [Sys. dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) para retornar informações sobre todas as esperas encontradas por threads executados durante a operação. Você pode usar essa exibição agregada para diagnosticar problemas de desempenho com o banco de dados SQL do Azure e também com consultas e lotes específicos.
- Use [Sys. dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) para retornar informações sobre a fila de tarefas que estão aguardando algum recurso.

Em cenários de alta CPU, Repositório de Consultas e estatísticas de espera podem não refletir o uso da CPU se:

- As consultas de alto consumo de CPU ainda estão em execução.
- As consultas de alto consumo de CPU estavam sendo executadas quando um failover ocorreu.

DMVs que rastreiam Repositório de Consultas e estatísticas de espera mostram resultados somente para consultas concluídas com êxito e tempo limite. Eles não mostram dados para instruções atualmente em execução até que as instruções sejam concluídas. Use a exibição de gerenciamento dinâmico [Sys. dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) para controlar as consultas em execução no momento e a hora de trabalho associada.

O gráfico próximo ao início deste artigo mostra que as esperas mais comuns são:

- Bloqueios (bloqueio)
- E/S
- Contenção relacionada ao TempDB
- Esperas de concessão de memória

> [!IMPORTANT]
> Para obter um conjunto de consultas T-SQL que usam DMVs para solucionar problemas relacionados à espera, consulte:
>
> - [Identificar problemas de desempenho de e/s](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identificar esperas de concessão de memória](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)
> - [Esperas e travas do TigerToolbox](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [Usp_whatsup TigerToolbox](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="improve-database-performance-with-more-resources"></a>Melhorar o desempenho do banco de dados com mais recursos

Se nenhum item acionável puder melhorar o desempenho do banco de dados, você poderá alterar a quantidade de recursos disponíveis no banco de dados SQL do Azure. Atribua mais recursos alterando a [camada de serviço de DTU](sql-database-service-tiers-dtu.md) de um único banco de dados. Ou aumente o eDTUs de um pool elástico a qualquer momento. Como alternativa, se você estiver usando o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md), altere a camada de serviço ou aumente os recursos alocados para o banco de dados.

Para bancos de dados individuais, você pode [alterar as camadas de serviço ou os recursos de computação](sql-database-single-database-scale.md) sob demanda para melhorar o desempenho do banco de dados. Para vários bancos de dados, considere o uso de [pools elásticos](sql-database-elastic-pool-guidance.md) para dimensionar os recursos automaticamente.

## <a name="tune-and-refactor-application-or-database-code"></a>Ajustar e refatorar o aplicativo ou o código do banco de dados

Você pode otimizar o código do aplicativo para o banco de dados, alterar índices, forçar planos ou usar dicas para adaptar manualmente o banco de dados à sua carga de trabalho. Para obter informações sobre o ajuste manual e a reescrita do código, consulte [diretrizes de ajuste de desempenho](sql-database-performance-guidance.md).

## <a name="next-steps"></a>Passos seguintes

- Para habilitar o ajuste automático no banco de dados SQL do Azure e permitir que o recurso de ajuste automático gerencie totalmente sua carga de trabalho, consulte [habilitar o ajuste automático](sql-database-automatic-tuning-enable.md).
- Para usar o ajuste manual, examine [as recomendações de ajuste no portal do Azure](sql-database-advisor-portal.md). Aplique manualmente as recomendações que melhoram o desempenho de suas consultas.
- Altere os recursos que estão disponíveis no banco de dados alterando as [camadas de serviço do banco de dados SQL do Azure](sql-database-performance-guidance.md).
