---
title: Monitoramento & ajuste de desempenho – banco de dados SQL do Azure | Microsoft Docs
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
ms.openlocfilehash: ee4bd9d61856ef4ea1afdd027d6f39e730b92d78
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129209"
---
# <a name="monitoring-and-performance-tuning"></a>Ajuste de monitorização e desempenho

O banco de dados SQL do Azure fornece ferramentas e métodos para monitorar facilmente o uso e adicionar ou remover recursos (CPU, memória, e/s), solucionar problemas potenciais e fazer recomendações para melhorar o desempenho de um banco de dados. O banco de dados SQL do Azure tem recursos que podem corrigir automaticamente os problemas nos bancos. O ajuste automático permite que um banco de dados se adapte à carga de trabalho e otimize automaticamente o desempenho. No entanto, há alguns problemas personalizados que podem precisar de solução de problemas. Este artigo explica algumas práticas recomendadas e ferramentas que podem ser usadas para solucionar problemas de desempenho.

Há duas atividades principais que devem ser feitas para garantir que um banco de dados esteja sendo executado sem problemas:
- [Monitore o desempenho do banco de dados](#monitoring-database-performance) para garantir que os recursos atribuídos ao banco de dados possam lidar com a carga de trabalho. Se você vir que um banco de dados está atingindo os limites de recurso, considere:
   - identificando e otimizando as principais consultas de consumo de recursos.
   - adicionar mais recursos atualizando a camada de serviço.
- [Solucionar problemas de desempenho](#troubleshoot-performance-issues) para identificar por que um problema potencial ocorreu, identificar a causa raiz do problema. Depois que a causa raiz for determinada, implemente as etapas para corrigir o problema.

## <a name="monitoring-database-performance"></a>Monitorando o desempenho do banco de dados

O monitoramento do desempenho de um banco de dados SQL no Azure começa com o monitoramento dos recursos usados em relação ao nível escolhido de desempenho do banco de dados. Os seguintes recursos devem ser monitorados:
 - **Uso da CPU** -Verifique se o banco de dados está atingindo 100% do uso da CPU por um longo período de tempo. O alto uso da CPU pode indicar que as consultas que usam a maior capacidade de computação devem ser identificadas e ajustadas. Ou, o alto uso da CPU pode indicar que o banco de dados ou a instância deve ser atualizada para uma camada de serviço superior. 
 - **Estatísticas de espera** -use [Sys. dm _os_wait_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) para determinar as esperas que as consultas estão ocorrendo. As consultas podem estar aguardando recursos, esperas de fila ou esperas externas. 
 - **Uso de e/s** -Verifique se o banco de dados está atingindo os limites de e/s do armazenamento subjacente.
 - **Uso de memória** -a quantidade de memória disponível para o banco de dados ou instância é proporcional ao número de vCores. Verifique se a memória é suficiente para a carga de trabalho. A expectativa de vida da página é um dos parâmetros que podem indicar a rapidez com que as páginas são removidas da memória.

O serviço de banco de dados SQL do Azure **inclui as ferramentas e os recursos para ajudar a solucionar problemas de desempenho potenciais**. As oportunidades podem ser identificadas para melhorar e otimizar o desempenho da consulta sem alterar os recursos, revisando as [recomendações de ajuste de desempenho](sql-database-advisor.md). Os índices em falta e as consultas mal otimizadas são razões comuns para um desempenho fraco da base de dados. Essas recomendações de ajuste podem ser aplicadas para melhorar o desempenho da carga de trabalho. Também podemos permitir que o banco de dados SQL do Azure [otimize automaticamente o desempenho das consultas](sql-database-automatic-tuning.md) aplicando todas as recomendações identificadas e verificando se as recomendações melhoraram o desempenho do banco de dados.

As opções a seguir estão disponíveis para monitoramento e solução de problemas de desempenho do banco de dados:

- No [portal do Azure](https://portal.azure.com), clique em **bancos**de dados SQL, selecione o banco e, em seguida, use o gráfico de monitoramento para procurar recursos que se aproximam da utilização máxima. O consumo de DTU é mostrado por padrão. Clique em **Editar** para alterar o intervalo de tempo e os valores mostrados.
- Ferramentas como SQL Server Management Studio fornecem muitos relatórios úteis, como um [painel de desempenho](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard?view=sql-server-2017) , para monitorar a utilização de recursos e identificar as principais consultas de consumo de recursos. [Repositório de consultas](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed) pode ser usado para identificar consultas com desempenho regressivo.
- Use [análise de desempenho de consultas](sql-database-query-performance.md) no [portal do Azure](https://portal.azure.com) para identificar as consultas que utilizam a maioria dos recursos. Esse recurso está disponível somente em pools elásticos e Banco de Dados Individual.
- Use [Assistente do banco de dados SQL](sql-database-advisor-portal.md) para exibir recomendações para criar e descartar índices, parametrizar consultas e corrigir problemas de esquema. Esse recurso está disponível somente em pools elásticos e Banco de Dados Individual.
- Use o [Azure SQL Intelligent insights](sql-database-intelligent-insights.md) para o monitoramento automático do desempenho do banco de dados. Depois que um problema de desempenho é detectado, um log de diagnóstico é gerado com detalhes e a RCA (análise de causa raiz) do problema. A recomendação de melhoria de desempenho é fornecida quando possível.
- [Habilite o ajuste automático](sql-database-automatic-tuning-enable.md) e deixe que o banco de dados SQL do Azure corrija automaticamente os problemas de desempenho identificados.
- Use [DMVs (exibições de gerenciamento dinâmico)](sql-database-monitoring-with-dmvs.md), [eventos estendidos](sql-database-xevent-db-diff-from-svr.md)e o [repositório de consultas](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) para solução de problemas de desempenho mais detalhada.

> [!TIP]
> Consulte [diretrizes de desempenho](sql-database-performance-guidance.md) para encontrar técnicas que você pode usar para melhorar o desempenho do banco de dados SQL do Azure depois de identificar o problema de desempenho usando um ou mais dos métodos acima.

## <a name="troubleshoot-performance-issues"></a>Resolver problemas de desempenho

Para diagnosticar e resolver problemas de desempenho, comece compreendendo o estado de cada consulta ativa e as condições que causam problemas de desempenho relevantes para cada Estado de carga de trabalho. Para melhorar o desempenho do banco de dados SQL do Azure, entenda que cada solicitação de consulta ativa do aplicativo está em um estado em execução ou em espera. Ao solucionar um problema de desempenho no banco de dados SQL do Azure, lembre-se do seguinte gráfico ao ler este artigo para diagnosticar e resolver problemas de desempenho.

![Estados de carga de trabalho](./media/sql-database-monitor-tune-overview/workload-states.png)

Para uma carga de trabalho com problemas de desempenho, o problema de desempenho pode ser devido à contenção de CPU (uma condição **relacionada à execução** ) ou a consultas individuais estão aguardando algo (uma condição **relacionada à espera** ).

As causas para problemas **relacionados à execução** podem ser:
- **Problemas de compilação** -o otimizador de consulta do SQL pode produzir um plano de qualidade inferior devido a estatísticas obsoletas, estimativa incorreta do número de linhas que serão processadas ou uma estimativa imprecisa da memória necessária. Se soubermos que a consulta foi executada mais rapidamente no passado ou em outra instância (Instância Gerenciada ou SQL Server instância), pegue os planos de execução reais e compare-os para ver se eles são diferentes. Tente aplicar dicas de consulta ou recriar estatísticas ou recriar índices para obter um plano melhor. Habilite a correção automática de plano no banco de dados SQL do Azure para atenuar esses problemas automaticamente.
- **Problemas de execução** – se o plano de consulta for ideal, ele provavelmente está atingindo os limites de recurso no banco de dados, como a taxa de transferência de gravação de log ou está usando índices fragmentados que devem ser recriados. Um grande número de consultas simultâneas que precisam dos mesmos recursos também pode ser a causa de problemas de execução. Os problemas **relacionados à espera** na maioria dos casos estão relacionados aos problemas de execução, pois as consultas que não estão sendo executadas com eficiência provavelmente estão aguardando alguns recursos.

As causas de problemas **relacionados à espera** podem ser:
- **Bloqueio** – uma consulta pode manter o bloqueio em objetos no banco de dados enquanto outros estão tentando acessar os mesmos objetos. As consultas de bloqueio podem ser facilmente identificadas usando DMVs ou ferramentas de monitoramento.
- **Problemas de e/s** – as consultas podem estar aguardando que as páginas sejam gravadas nos arquivos de dados ou de log. Nesse caso, consulte `INSTANCE_LOG_RATE_GOVERNOR`, `WRITE_LOG`ou `PAGEIOLATCH_*` aguarde estatísticas no DMV.
- **Problemas de tempdb** – se a carga de trabalho usar tabelas temporárias ou se houver despejos de tempdb nos planos, as consultas poderão ter um problema com a taxa de transferência de tempdb. 
- **Problemas relacionados à memória** – pode não haver memória suficiente para a carga de trabalho para que a expectativa de vida da página possa ser descartada ou as consultas estejam ficando menos memória do que o necessário. Em alguns casos, a inteligência interna no otimizador de consulta corrigirá esses problemas.
 
 As seções a seguir explicarão como identificar e solucionar alguns desses problemas.

## <a name="running-related-performance-issues"></a>Problemas de desempenho relacionados à execução

Como uma diretriz geral, se a utilização da CPU for consistente ou acima de 80%, haverá um problema de desempenho relacionado à execução. Se houver um problema relacionado à execução, ele pode ser causado por recursos insuficientes de CPU ou pode estar relacionado a uma das seguintes condições:

- Muitas consultas em execução
- Muitas consultas de compilação
- Uma ou mais consultas em execução estão usando um plano de consulta de qualidade inferior

Se for determinado que há um problema de desempenho relacionado à execução, o objetivo é identificar o problema preciso usando um ou mais métodos. Os métodos mais comuns para identificar problemas relacionados à execução são:

- Use o [portal do Azure](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal) para monitorar a utilização da porcentagem de CPU.
- Use as seguintes [exibições de gerenciamento dinâmico](sql-database-monitoring-with-dmvs.md):

  - [Sys. dm _db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) retorna CPU, e/s e consumo de memória para um banco de dados SQL do Azure. Existe uma linha para cada intervalo de 15 segundos, mesmo se não houver nenhuma atividade no banco de dados. Os dados históricos são mantidos por uma hora.
  - [Sys. resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) retorna o uso de CPU e dados de armazenamento para um banco de dado SQL do Azure. Os dados são coletados e agregados em intervalos de cinco minutos.

> [!IMPORTANT]
> Para consultas T-SQL usando sys. dm _db_resource_stats e sys. resource_stats DMVs para solucionar problemas de utilização da CPU, consulte [identificar problemas de desempenho da CPU](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues).

### <a name="ParamSniffing"></a>Solucionar problemas de consultas com problemas de plano de execução de consulta sensível a parâmetros

O problema do plano sensível ao parâmetro (PSP) refere-se a um cenário em que o otimizador de consulta gera um plano de execução de consulta que é ideal apenas para um valor de parâmetro específico (ou conjunto de valores) e o plano em cache não é ideal para os valores de parâmetro usados em execuções consecutivas. Os planos não ideais podem resultar em problemas de desempenho de consulta e degradação geral da taxa de transferência da carga de trabalho. Para obter mais informações sobre detecção de parâmetros e processamento de consultas, consulte o [Guia de arquitetura de processamento de consultas](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

Há várias soluções alternativas usadas para mitigar esses problemas, cada um com desvantagens e concessões associadas:

- Use a [](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) dica de consulta recompile em cada execução de consulta. Essa solução alternativa compensa o tempo de compilação e a CPU aumentada para uma melhor qualidade de plano. Usar a `RECOMPILE` opção geralmente não é possível para cargas de trabalho que exigem uma alta taxa de transferência.
- Use a dica de consulta [Option (Optimize for...)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) para substituir o valor do parâmetro real por um valor de parâmetro típico que produz um bom plano suficiente para a maioria das possibilidades de valor de parâmetro.   Essa opção requer uma boa compreensão dos valores de parâmetro ideais e das características do plano associado.
- Use a dica de consulta [opção (otimizar para desconhecido)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) para substituir o valor real do parâmetro no Exchange para usar a média do vetor de densidade. Outra maneira de fazer isso é capturando os valores de parâmetro de entrada em variáveis locais e, em seguida, usando as variáveis locais dentro dos predicados em vez de usar os próprios parâmetros. A densidade média deve ser *boa o suficiente* com essa correção específica.
- Desabilite a detecção de parâmetros totalmente usando a dica de consulta [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) .
- Use a dica de consulta [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) para evitar recompilações no cache. Essa solução alternativa pressupõe que o plano comum *bom o suficiente* é o já existente no cache. Você também pode desabilitar as atualizações automáticas para estatísticas a fim de reduzir a chance de o bom plano ser removido e um novo plano incorreto que está sendo compilado.
- Force o plano usando explicitamente a dica de consulta [use Plan](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) (especificando explicitamente, definindo um plano específico usando repositório de consultas ou habilitando o [ajuste automático](sql-database-automatic-tuning.md).
- Substitua o único procedimento por um conjunto aninhado de procedimentos que podem ser usados com base em lógica condicional e os valores de parâmetro associados.
- Crie alternativas de execução de cadeia de caracteres dinâmica para uma definição de procedimento estático.

Para obter mais informações sobre como resolver esses tipos de problemas, consulte as postagens no blog:

- [Eu Smell um parâmetro](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/)
- [SQL dinâmico versus qualidade do plano para consultas parametrizadas](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)
- [Técnicas de otimização de consulta SQL no SQL Server: Detecção de parâmetros](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="troubleshooting-compile-activity-due-to-improper-parameterization"></a>Solução de problemas de atividade de compilação devido à parametrização incorreta

Quando uma consulta tem literais, o mecanismo de banco de dados escolhe parametrizar automaticamente a instrução ou um usuário pode parametrizar explicitamente a seqüência para reduzir o número de compilações. Um número alto de compilações para uma consulta usando o mesmo padrão, mas valores literais diferentes podem resultar em alta utilização da CPU. Da mesma forma, se você apenas parcialmente parametrizar uma consulta que continua a ter literais, o mecanismo de banco de dados não o parametrizará mais.  Veja abaixo um exemplo de uma consulta com parâmetros parcialmente:

```sql
SELECT * 
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

No exemplo anterior, `t1.c1` o leva `@p1` mas `t2.c2` continua a pegar o GUID como literal. Nesse caso, se você alterar o valor de `c2`, a consulta será tratada como uma consulta diferente e ocorrerá uma nova compilação. Para reduzir as compilações no exemplo anterior, a solução também é parametrizar o GUID.

A consulta a seguir mostra a contagem de consultas por hash de consulta para determinar se uma consulta está parametrizada corretamente ou não:

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
### <a name="factors-influencing-query-plan-changes"></a>Fatores que influenciam as alterações do plano de consulta

Uma recompilação do plano de execução de consulta pode resultar em um plano de consulta gerado que difere do que foi originalmente armazenado em cache. Há várias razões pelas quais um plano original existente pode ser recompilado automaticamente:
- Alterações no esquema que está sendo referenciado pela consulta
- Alterações de dados nas tabelas que estão sendo referenciadas pela consulta 
- Alterações nas opções de contexto de consulta 

Um plano compilado pode ser ejetado do cache por vários motivos, incluindo reinicializações de instância, alterações de configuração no escopo do banco de dados, pressão de memória e solicitações explícitas para limpar o cache. Além disso, o uso de uma dica de recompilação significa que um plano não será armazenado em cache.

Uma recompilação (ou uma nova compilação após a remoção do cache) ainda pode resultar na geração de um plano de execução de consulta idêntico a partir daquele observado originalmente.  Se houver alterações no plano em comparação com o plano anterior ou original, veja a seguir as explicações mais comuns de por que um plano de execução de consulta foi alterado:

- **Design físico alterado**. Por exemplo, foram criados novos índices que abordam com mais eficiência os requisitos de uma consulta. Os novos índices poderão ser usados em uma nova compilação se o otimizador de consulta decidir que é mais adequado aproveitar esse novo índice do que usar a estrutura de dados selecionada originalmente para a primeira versão da execução da consulta.  Qualquer alteração física nos objetos referenciados pode resultar em uma nova opção de plano no momento da compilação.

- **Diferenças de recursos de servidor**. Em um cenário no qual um plano difere no "sistema A" vs. "sistema B" – a disponibilidade de recursos, como o número de processadores disponíveis, pode influenciar qual plano é gerado.  Por exemplo, se um sistema tiver um número maior de processadores, um plano paralelo poderá ser escolhido. 

- **Estatísticas diferentes**. As estatísticas associadas aos objetos referenciados foram alteradas ou são materiais diferentes das estatísticas do sistema original.  Se as estatísticas forem alteradas e uma recompilação ocorrer, o otimizador de consulta usará as estatísticas desse ponto específico no tempo. As estatísticas revisadas podem ter diferentes distribuições de dados e frequências do que a compilação original.  Essas alterações são usadas para estimar estimativas de cardinalidade (número de linhas previstas para fluir pela árvore de consulta lógica).  As alterações nas estimativas de cardinalidade podem nos levar a escolher diferentes operadores físicos e ordens de operações associadas.  Até mesmo alterações secundárias em estatísticas podem resultar em um plano de execução de consulta alterado.

- **Nível de compatibilidade do banco de dados alterado ou versão do avaliador de cardinalidade**.  As alterações no nível de compatibilidade do banco de dados podem habilitar novas estratégias e recursos que podem resultar em um plano de execução de consulta diferente.  Além do nível de compatibilidade do banco de dados, a desabilitação ou a habilitação do sinalizador de rastreamento 4199 ou a alteração do estado da configuração do escopo do banco de dados QUERY_OPTIMIZER_HOTFIXES também pode influenciar as opções do plano de execução de consulta em tempo de  Os sinalizadores de rastreamento 9481 (forçar herança CE) e 2312 (forçar padrão CE) também são planos que afetam. 

### <a name="resolve-problem-queries-or-provide-more-resources"></a>Resolver consultas de problemas ou fornecer mais recursos

Depois de identificar o problema, você pode ajustar as consultas de problema ou atualizar o tamanho da computação ou a camada de serviço para aumentar a capacidade de seu banco de dados SQL do Azure para absorver os requisitos de CPU. Para obter informações sobre o dimensionamento de recursos para bancos de dados individuais, consulte [dimensionar recursos de banco de dados individual no banco de dados SQL do Azure](sql-database-single-database-scale.md) e para dimensionar recursos para pools elásticos, consulte [dimensionar recursos do pool elástico no banco de dados SQL do Azure](sql-database-elastic-pool-scale.md) Para obter informações sobre como dimensionar uma instância gerenciada, consulte [limites de recursos em nível de instância](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits).

### <a name="determine-if-running-issues-due-to-increase-workload-volume"></a>Determinar se problemas em execução devido ao aumento do volume de carga de trabalho

Um aumento no tráfego e na carga de trabalho do aplicativo pode considerar o aumento da utilização da CPU, mas você deve ter cuidado para diagnosticar corretamente esse problema. Em um cenário de alta CPU, responda a essas perguntas para determinar se, de fato, um aumento de CPU é devido a alterações no volume da carga de trabalho:

1. As consultas do aplicativo são a causa do problema de alta CPU?
2. Para as principais consultas que consomem CPU (que podem ser identificadas):

   - Determine se havia vários planos de execução associados à mesma consulta. Nesse caso, determine o porquê.
   - Para consultas com o mesmo plano de execução, determine se os tempos de execução foram consistentes e se a contagem de execução aumentou. Em caso afirmativo, provavelmente há problemas de desempenho devido ao aumento da carga de trabalho.

Para resumir, se o plano de execução de consulta não foi executado de forma diferente, mas a utilização da CPU aumentou com a contagem de execução, é provável que haja um problema de desempenho relacionado à carga de trabalho.

Nem sempre é fácil concluir que há uma alteração de volume de carga de trabalho que está gerando um problema de CPU.   Fatores a considerar: 

- **Uso de recursos alterado**

  Por exemplo, considere um cenário em que a CPU aumentou para 80% por um longo período de tempo.  A utilização da CPU sozinha não significa que o volume da carga de trabalho mudou.  As regressões do plano de execução de consulta e as alterações de distribuição de dados também podem contribuir para mais utilização de recursos, embora o aplicativo esteja executando a mesma carga de trabalho exata.

- **Nova consulta exibida**

   Um aplicativo pode gerar um novo conjunto de consultas em momentos diferentes.

- **Número de solicitações aumentadas ou diminuídas**

   Esse cenário é a medida mais óbvia da carga de trabalho. O número de consultas nem sempre corresponde a mais utilização de recursos. No entanto, essa métrica ainda é um sinal significativo, supondo que outros fatores estejam inalterados.

## <a name="waiting-related-performance-issues"></a>Problemas de desempenho relacionados à espera

Depois de ter certeza de que você não está enfrentando um problema de desempenho relacionado à execução de alta CPU, você está enfrentando um problema de desempenho relacionado à espera. Ou seja, os recursos de CPU não estão sendo usados com eficiência porque a CPU está aguardando algum outro recurso. Nesse caso, a próxima etapa é identificar o que os recursos de CPU estão aguardando. Os métodos mais comuns para mostrar as categorias de tipo de espera principais são:

- O [repositório de consultas](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) fornece estatísticas de espera por consulta ao longo do tempo. Em Repositório de Consultas, os tipos de espera são combinados em categorias de espera. O mapeamento de categorias de espera para tipos de espera está disponível em [Sys. query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- [Sys. dm _db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) retorna informações sobre todas as esperas encontradas por threads executados durante a operação. Você pode usar essa exibição agregada para diagnosticar problemas de desempenho com o banco de dados SQL do Azure e também com consultas e lotes específicos.
- [Sys. dm _os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) retorna informações sobre a fila de espera de tarefas que estão aguardando algum recurso.

Em cenários de alta CPU, as estatísticas de Repositório de Consultas e espera nem sempre refletem a utilização da CPU por esses dois motivos:

- As consultas de consumo de alta CPU ainda podem estar em execução e as consultas não foram concluídas
- As consultas de consumo de alta CPU estavam em execução quando ocorreu um failover

Repositório de Consultas e aguardar estatísticas – acompanhamento de exibições de gerenciamento dinâmico mostra apenas os resultados para consultas concluídas e expiradas com êxito e não mostra dados para instruções em execução no momento (até que elas sejam concluídas). A exibição de gerenciamento dinâmico [Sys. dm _exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) permite que você acompanhe consultas em execução no momento e a hora de trabalho associada.

Conforme mostrado no gráfico anterior, as esperas mais comuns são:

- Bloqueios (bloqueio)
- E/S
- `tempdb`-contenção relacionada
- Esperas de concessão de memória

> [!IMPORTANT]
> Para um conjunto de consultas T-SQL usando essas DMVs para solucionar esses problemas relacionados à espera, consulte:
>
> - [Identificar problemas de desempenho de e/s](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identificar `tempdb` problemas de desempenho](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identificar esperas de concessão de memória](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)
> - [TigerToolbox-esperas e travas](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox - usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="improving-database-performance-with-more-resources"></a>Melhorando o desempenho do banco de dados com mais recursos

Por fim, se não houver itens acionáveis que possam melhorar o desempenho do banco de dados, você poderá alterar a quantidade de recursos disponíveis no banco de dados SQL do Azure. Atribua mais recursos alterando a [camada de serviço de DTU](sql-database-service-tiers-dtu.md) de um banco de dados individual ou aumente o eDTUs de um pool elástico a qualquer momento. Como alternativa, se você estiver usando o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md), altere a camada de serviço ou aumente os recursos alocados para o banco de dados.

1. Para bancos de dados individuais, você pode [alterar as camadas de serviço](sql-database-single-database-scale.md) ou os [recursos de computação](sql-database-single-database-scale.md) sob demanda para melhorar o desempenho do banco de dados.
2. Para vários bancos de dados, considere o [](sql-database-elastic-pool-guidance.md) uso de pools elásticos para dimensionar os recursos automaticamente.

## <a name="tune-and-refactor-application-or-database-code"></a>Ajustar e refatorar o aplicativo ou o código do banco de dados

Você pode alterar o código do aplicativo para usar melhor o banco de dados, alterar índices, forçar planos ou usar dicas para adaptar manualmente o banco de dados à sua carga de trabalho. Encontre orientações e dicas para ajuste manual e reescrever o código no artigo de [diretrizes de desempenho](sql-database-performance-guidance.md) .

## <a name="next-steps"></a>Passos Seguintes

- Para habilitar o ajuste automático no banco de dados SQL do Azure e deixar o recurso de ajuste automático gerenciar totalmente sua carga de trabalho, consulte [habilitar o ajuste automático](sql-database-automatic-tuning-enable.md).
- Para usar o ajuste manual, você pode examinar as [recomendações de ajuste em portal do Azure](sql-database-advisor-portal.md) e aplicá-las manualmente que melhoram o desempenho de suas consultas.
- Alterar os recursos que estão disponíveis no banco de dados alterando as [camadas de serviço do banco de dados SQL do Azure](sql-database-performance-guidance.md)
