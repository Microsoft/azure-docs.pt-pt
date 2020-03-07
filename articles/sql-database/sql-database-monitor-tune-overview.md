---
title: Ajuste de monitorização e desempenho
description: Dicas para a finação de desempenho na Base de Dados Azure SQL através de avaliação e melhoria.
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
ms.openlocfilehash: e77af00dc3352af3265da90685e58b34c96bee81
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78382351"
---
# <a name="monitoring-and-performance-tuning"></a>Ajuste de monitorização e desempenho

A Base de Dados Azure SQL fornece ferramentas e métodos que pode utilizar para monitorizar facilmente o uso, adicionar ou remover recursos (como CPU, memória ou I/O), resolver problemas potenciais e fazer recomendações para melhorar o desempenho de uma base de dados. As funcionalidades na Base de Dados Azure SQL podem corrigir automaticamente problemas nas bases de dados. 

A afinação automática permite que uma base de dados se adapte à carga de trabalho e otimize automaticamente o desempenho. No entanto, alguns problemas personalizados podem precisar de resolução de problemas. Este artigo explica algumas boas práticas e algumas ferramentas que pode usar para resolver problemas de desempenho.

Para garantir que uma base de dados funciona sem problemas, deve:
- [Monitorize](#monitor-database-performance) o desempenho da base de dados para se certificar de que os recursos atribuídos à base de dados podem lidar com a carga de trabalho. Se a base de dados estiver a atingir os limites dos recursos, considere:
   - Identificar e otimizar as principais consultas de consumo de recursos.
   - Adicionando mais recursos através [da atualização do nível de serviço.](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources)
- [Problemas de desempenho](#troubleshoot-performance-problems) para identificar o porquê de um problema potencial ocorrer e identificar a causa principal do problema. Depois de identificar a causa principal, tome medidas para corrigir o problema.

## <a name="monitor-database-performance"></a>Monitorizar o desempenho da base de dados

Para monitorizar o desempenho de uma base de dados SQL em Azure, comece por monitorizar os recursos utilizados em relação ao nível de desempenho da base de dados que escolheu. Monitorize os seguintes recursos:
 - **Utilização**do CPU : Verifique se a base de dados está a atingir 100% do uso do CPU durante um longo período de tempo. Uma utilização elevada do CPU pode indicar que é necessário identificar e afinar consultas que usam mais poder de computação. Uma utilização elevada do CPU também pode indicar que a base de dados ou a instância devem ser atualizadas para um nível de serviço mais elevado. 
 - **Estatísticas de espera**: Utilize [sys.dm_os_wait_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) para determinar quanto tempo as consultas estão à espera. As consultas podem estar à espera de recursos, esperas de fila ou esperas externas. 
 - **Utilização IO**: Verifique se a base de dados está a atingir os limites de IO do armazenamento subjacente.
 - **Utilização da memória**: A quantidade de memória disponível para a base de dados ou instância é proporcional ao número de vCores. Certifique-se de que a memória é suficiente para a carga de trabalho. A esperança de vida da página é um dos parâmetros que pode indicar a rapidez com que as páginas são removidas da memória.

O serviço de base de dados Azure SQL inclui ferramentas e recursos para ajudá-lo a resolver problemas e corrigir potenciais problemas de desempenho. Pode identificar oportunidades para melhorar e otimizar o desempenho da consulta sem alterar recursos através da revisão de recomendações de [afinação](sql-database-advisor.md)de desempenho. 

Os índices em falta e as consultas mal otimizadas são razões comuns para um desempenho fraco da base de dados. Pode aplicar recomendações de afinação para melhorar o desempenho da carga de trabalho. Também pode permitir que a Base de Dados Azure SQL [otimize automaticamente o desempenho das consultas](sql-database-automatic-tuning.md) aplicando todas as recomendações identificadas. Em seguida, verifique se as recomendações melhoraram o desempenho da base de dados.

> [!NOTE]
> A indexação está disponível apenas na base de dados e piscinas elásticas. A indexação não está disponível num caso gerido.

Escolha entre as seguintes opções para monitorizar e resolver o desempenho da base de dados:

- No [portal Azure,](https://portal.azure.com)selecione bases de **dados SQL** e selecione a base de dados. Na tabela **de monitorização,** procure recursos que se aproximem da sua utilização máxima. O consumo de DTU é mostrado por padrão. Selecione **Editar** para alterar o intervalo de tempo e os valores mostrados.
- Ferramentas como o SQL Server Management Studio fornecem muitos relatórios úteis, como [o Painel de Desempenho.](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard) Utilize estes relatórios para monitorizar a utilização de recursos e identificar as principais consultas que consomem recursos. Pode utilizar a [Consulta Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed) para identificar consultas cujo desempenho regrediu.
- No [portal Azure,](https://portal.azure.com)utilize a [Consulta Performance Insight](sql-database-query-performance.md) para identificar as consultas que mais utilizam. Esta funcionalidade está disponível apenas em bases de dados e piscinas elásticas.
- Utilize o [SQL Database Advisor](sql-database-advisor-portal.md) para visualizar recomendações para ajudá-lo a criar e largar índices, parametrizar consultas e corrigir problemas de esquema. Esta funcionalidade está disponível apenas em bases de dados e piscinas elásticas.
- Utilize [insights inteligentes Azure SQL](sql-database-intelligent-insights.md) para monitorizar automaticamente o desempenho da base de dados. Quando um problema de desempenho é detetado, é gerado um registo de diagnóstico. O registo fornece detalhes e uma análise de causa sela (RCA) do problema. Uma recomendação de melhoria do desempenho é fornecida sempre que possível.
- Ative a [sintonização automática](sql-database-automatic-tuning-enable.md) para permitir que a Base de Dados Azure SQL corrija automaticamente os problemas de desempenho.
- Utilize pontos de vista dinâmicos de [gestão (DMVs),](sql-database-monitoring-with-dmvs.md) [eventos alargados](sql-database-xevent-db-diff-from-svr.md)e [Loja de Consulta](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) para ajudar com resolução mais detalhada de problemas de desempenho.

> [!TIP]
> Depois de identificar um problema de desempenho, consulte a nossa [orientação](sql-database-performance-guidance.md) de desempenho para encontrar técnicas para melhorar o desempenho da Base de Dados Azure SQL.

## <a name="troubleshoot-performance-problems"></a>Problemas de desempenho de resolução de problemas

Para diagnosticar e resolver problemas de desempenho, comece por descobrir o estado de cada consulta ativa e as condições que causam problemas de desempenho relevantes para cada estado de carga de trabalho. Para melhorar o desempenho da Base de Dados Azure SQL, precisa entender que cada pedido de consulta ativa da aplicação está em estado de execução ou em estado de espera. À medida que resolver um problema de desempenho na Base de Dados Azure SQL, tenha em mente o seguinte diagrama.

![Estados de carga de trabalho](./media/sql-database-monitor-tune-overview/workload-states.png)

Um problema de desempenho numa carga de trabalho pode ser causado pela contenção do CPU (uma condição relacionada com o *funcionamento)* ou consultas individuais que aguardam algo (uma condição *relacionada com a espera).*

Problemas relacionados com o funcionamento podem ser causados por:
- **Problemas**de compilação : O Optimizador de Consulta SQL pode produzir um plano sub-ideal devido a estatísticas velhas, uma estimativa incorreta do número de linhas a processar, ou uma estimativa imprecisa da memória necessária. Se souber que a consulta foi executada mais rapidamente no passado ou em outro caso (ou uma instância gerida ou uma instância de SQL Server), compare os planos de execução reais para ver se são diferentes. Tente aplicar dicas de consulta ou reconstruir estatísticas ou índices para obter o melhor plano. Ative a correção automática do plano na Base de Dados Azure SQL para mitigar automaticamente estes problemas.
- **Problemas de execução**: Se o plano de consulta for o ideal, provavelmente está a atingir os limites de recursos da base de dados, como a entrada de log write. Ou pode estar a usar índices fragmentados que devem ser reconstruídos. Os problemas de execução também podem ocorrer quando um grande número de consultas simultâneas precisam dos mesmos recursos. Os problemas *relacionados com a espera* estão geralmente relacionados com problemas de execução, porque as consultas que não executam eficientemente estão provavelmente à espera de alguns recursos.

Problemas relacionados com a espera podem ser causados por:
- **Bloqueio**: Uma consulta pode manter o bloqueio nos objetos na base de dados enquanto outros tentam aceder aos mesmos objetos. Pode identificar consultas de bloqueio utilizando DMVs ou ferramentas de monitorização.
- **Problemas iO**: As consultas podem estar à espera que as páginas sejam escritas nos dados ou ficheiros de registo. Neste caso, verifique as estatísticas de `INSTANCE_LOG_RATE_GOVERNOR`, `WRITE_LOG`ou `PAGEIOLATCH_*` de espera no DMV.
- **Problemas tempDB**: Se a carga de trabalho utilizar tabelas temporárias ou houver derrames tempDB nos planos, as consultas podem ter um problema com a entrada tempDB. 
- **Problemas relacionados**com a memória : Se a carga de trabalho não tiver memória suficiente, a esperança de vida da página pode diminuir, ou as consultas podem obter menos memória do que precisam. Em alguns casos, a inteligência incorporada no Optimizer de Consulta resolverá problemas relacionados com a memória.
 
As seguintes secções explicam como identificar e resolver problemas.

## <a name="performance-problems-related-to-running"></a>Problemas de desempenho relacionados com a execução

Como orientação geral, se o uso de CPU estiver consistentemente a 80%, o seu problema de desempenho está relacionado com o funcionamento. Um problema relacionado com o funcionamento pode ser causado por recursos cpu insuficientes. Ou pode estar relacionado com uma das seguintes condições:

- Demasiadas consultas de execução
- Demasiadas consultas de compilação
- Uma ou mais consultas de execução que usam um plano de consulta sub-ideal

Se encontrar um problema de desempenho relacionado com a corrida, o seu objetivo é identificar o problema preciso utilizando um ou mais métodos. Estes métodos são as formas mais comuns de identificar problemas relacionados com o funcionamento:

- Utilize o [portal Azure](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal) para monitorizar a utilização percentual da CPU.
- Utilize os seguintes [DMVs:](sql-database-monitoring-with-dmvs.md)

  - O [DMV sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) devolve CPU, I/O e consumo de memória para uma base de dados SQL. Existe uma linha para cada intervalo de 15 segundos, mesmo que não haja atividade na base de dados. Os dados históricos são mantidos durante uma hora.
  - O [DMV sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) devolve dados de utilização e armazenamento de CPU para a Base de Dados Azure SQL. Os dados são recolhidos e agregados em intervalos de cinco minutos.

> [!IMPORTANT]
> Para resolver problemas de utilização do CPU para consultas T-SQL que utilizam os DMVs sys.dm_db_resource_stats e sys.resource_stats, consulte identificar problemas de [desempenho do CPU](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues).

### <a name="ParamSniffing"></a>Consultas que têm problemas com PSP

Um problema de plano sensível parametro (PSP) acontece quando o optimizador de consultas gera um plano de execução de consulta que é ideal apenas para um valor de parâmetro específico (ou conjunto de valores) e o plano cached não é então ideal para valores de parâmetros que são usados em consecutivas execuções. Planos que não são ideais podem então causar problemas de desempenho de consulta e degradar a carga de trabalho geral. 

Para obter mais informações sobre o cheiro dos parâmetros e o processamento de consultas, consulte o guia de [arquitetura de processamento de consultas.](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing)

Várias sobras podem atenuar problemas da PSP. Cada suposições tem trocas e inconvenientes associados:

- Utilize a dica de consulta [RECOMPILE](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) em cada execução de consulta. Esta suposição de suposição comercializa o tempo de compilação e o aumento da CPU para uma melhor qualidade do plano. A opção `RECOMPILE` muitas vezes não é possível para cargas de trabalho que requerem uma alta entrada.
- Use a [opção (OTIMIZE FOR...)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) dica para sobrepor o valor real do parâmetro com um valor de parâmetro típico que produz um plano que é bom o suficiente para a maioria das possibilidades de valor do parâmetro. Esta opção requer uma boa compreensão dos valores ideais dos parâmetros e das características do plano associados.
- Use a dica de [opção (OTIMIZE FOR UNKNOWN)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) para sobrepor ao valor real do parâmetro e, em vez disso, use a média do vetor de densidade. Também pode fazê-lo capturando os valores dos parâmetros de entrada em variáveis locais e, em seguida, usando as variáveis locais dentro dos predicados em vez de usar os próprios parâmetros. Para esta correção, a densidade média deve ser *suficientemente boa.*
- Desative o snifador do parâmetro utilizando a sugestão [de consulta DISABLE_PARAMETER_SNIFFING.](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)
- Utilize a sugestão de consulta [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) para evitar recompilações em cache. Esta suposição pressupõe que o plano comum já está em cache. Também pode desativar atualizações estatísticas automáticas para reduzir as hipóteses de o bom plano ser despejado e ser elaborado um novo plano mau.
- Force o plano usando explicitamente a dica de consulta DO [PLANO DE UTILIZAÇÃO](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) reescrevendo a consulta e adicionando a dica no texto de consulta. Ou defina um plano específico utilizando a Consulta Store ou permitindo a [afinação automática](sql-database-automatic-tuning.md).
- Substitua o procedimento único por um conjunto de procedimentos aninhados que cada um pode ser utilizado com base na lógica condicional e nos valores dos parâmetros associados.
- Criar alternativas dinâmicas de execução de cordas a uma definição de procedimento estático.

Para obter mais informações sobre a resolução de problemas da PSP, consulte estas publicações de blogue:

- [Sinto o cheiro de um parâmetro.](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/)
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

Neste exemplo, `t1.c1` leva `@p1`, mas `t2.c2` continua a tomar o GUID como literal. Neste caso, se alterar o valor para `c2`, a consulta é tratada como uma consulta diferente, e uma nova compilação acontecerá. Para reduzir as compilações neste exemplo, também parametria o GUID.

A seguinte consulta mostra a contagem de consultas por hash consulta para determinar se uma consulta é adequadamente parametrizada:

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

### <a name="factors-that-affect-query-plan-changes"></a>Fatores que afetam alterações no plano de consulta

Uma recompilação do plano de execução de consulta pode resultar num plano de consulta gerado que difere do plano original de cached. Um plano original existente pode ser automaticamente recompilado por várias razões:
- As alterações no esquema são referenciadas pela consulta.
- As alterações de dados nas tabelas são referenciadas pela consulta. 
- As opções de contexto de consulta foram alteradas.

Um plano compilado pode ser ejetado da cache por várias razões, tais como:

- O caso recomeça.
- Alterações de configuração com um espaço de base de dados.
- Pressão de memória.
- Pedidos explícitos para limpar a cache.

Se utilizar uma sugestão de RECOMPILE, um plano não será em cache.

Uma recompilação (ou compilação fresca após o despejo de cache) ainda pode resultar na geração de um plano de execução de consulta que é idêntico ao original. Quando o plano muda do plano anterior ou original, estas explicações são prováveis:

- **Design físico alterado**: Por exemplo, os índices recém-criados cobrem mais eficazmente os requisitos de uma consulta. Os novos índices podem ser usados numa nova compilação se o optimizador de consultas decidir que usar esse novo índice é mais ideal do que usar a estrutura de dados que foi originalmente selecionada para a primeira versão da execução da consulta.  Quaisquer alterações físicas nos objetos referenciados podem resultar numa nova escolha de plano no momento da compilação.

- **Diferenças de recursos**do servidor : Quando um plano num sistema difere do plano noutro sistema, a disponibilidade de recursos, como o número de processadores disponíveis, pode influenciar o plano gerado.  Por exemplo, se um sistema tiver mais processadores, poderá ser escolhido um plano paralelo. 

- **Estatísticas diferentes**: As estatísticas associadas aos objetos referenciados podem ter mudado ou podem ser materialmente diferentes das estatísticas do sistema original.  Se as estatísticas mudarem e uma recompilação acontecer, o optimizador de consultas utiliza as estatísticas a partir do momento em que mudaram. As distribuições e frequências de dados revistas das estatísticas podem diferir das da compilação original.  Estas mudanças são usadas para criar estimativas de cardinalidade. (As estimativas de*Cardinalidade* são o número de linhas que se espera que fluam através da árvore de consulta lógica.) As alterações às estimativas de cardinalidade podem levá-lo a escolher diferentes operadores físicos e ordens de operações associadas.  Mesmo pequenas alterações nas estatísticas podem resultar numa alteração no plano de execução da consulta.

- **Alteração do nível**de compatibilidade da base de dados ou versão do estimador de cardinalidade : Alterações ao nível de compatibilidade da base de dados podem permitir novas estratégias e funcionalidades que possam resultar num plano de execução de consultas diferente.  Para além do nível de compatibilidade da base de dados, uma bandeira de rastreio desativada ou ativada 4199 ou um estado alterado da configuração com aplicação de base de dados QUERY_OPTIMIZER_HOTFIXES também podem influenciar as escolhas do plano de execução de consultas no momento da compilação.  As bandeiras de vestígios 9481 (legado de força CE) e 2312 (força padrão CE) também afetam o plano. 

### <a name="resolve-problem-queries-or-provide-more-resources"></a>Resolver consultas de problemas ou fornecer mais recursos

Depois de identificar o problema, pode afinar as consultas de problemaou atualizar o tamanho da computação ou o nível de serviço para aumentar a capacidade da sua base de dados SQL para absorver os requisitos do CPU. 

Para mais informações, consulte os recursos de base de dados individuais da Scale na Base de [Dados Azure SQL](sql-database-single-database-scale.md) e na Escala de recursos de piscina elástica na Base de [Dados Azure SQL](sql-database-elastic-pool-scale.md). Para obter informações sobre a escala de uma instância gerida, consulte [os limites de recursos de nível de serviço](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

### <a name="performance-problems-caused-by-increased-workload-volume"></a>Problemas de desempenho causados pelo aumento do volume de trabalho

Um aumento do tráfego de aplicações e do volume de carga de trabalho pode causar um aumento da utilização do CPU. Mas deve ter cuidado para diagnosticar corretamente este problema. Quando vir um problema de alta CPU, responda a estas questões para determinar se o aumento é causado por alterações no volume de carga de trabalho:

- As perguntas da aplicação são a causa do problema da Alta CPU?
- Para as principais consultas que consomem CPU que você pode identificar:

   - Vários planos de execução foram associados à mesma consulta? Se sim, porquê?
   - Para consultas com o mesmo plano de execução, os tempos de execução foram consistentes? A contagem de execução aumentou? Em caso afirmativo, o aumento da carga de trabalho está provavelmente a causar problemas de desempenho.

Em resumo, se o plano de execução da consulta não executou de forma diferente, mas o uso de CPU aumentou juntamente com a contagem de execução, o problema de desempenho está provavelmente relacionado com um aumento da carga de trabalho.

Nem sempre é fácil identificar uma mudança de volume de carga que está a conduzir um problema de CPU. Considere estes fatores: 

- **Alteração da utilização**dos recursos : Por exemplo, considere um cenário em que o uso de CPU aumentou para 80 por cento durante um longo período de tempo.  O uso de CPU por si só não significa que o volume de carga de trabalho mudou. As regressões no plano de execução da consulta e as alterações na distribuição de dados também podem contribuir para uma maior utilização de recursos, mesmo que a aplicação execute a mesma carga de trabalho.

- **O aparecimento de uma nova consulta**: Uma aplicação pode conduzir um novo conjunto de consultas em diferentes momentos.

- **Aumento ou diminuição do número de pedidos**: Este cenário é a medida mais óbvia de uma carga de trabalho. O número de consultas nem sempre corresponde a mais utilização de recursos. No entanto, esta métrica ainda é um sinal significativo, assumindo que outros fatores se mantêm inalterados.

## <a name="waiting-related-performance-problems"></a>Problemas de desempenho relacionados com a espera 

Se tem a certeza de que o seu problema de desempenho não está relacionado com o elevado uso de CPU ou com a execução, o seu problema está relacionado com a espera. Ou seja, os seus recursos cpu não estão a ser usados de forma eficiente porque a CPU está à espera de outro recurso. Neste caso, identifique o que os seus recursos da CPU estão à espera. 

Estes métodos são comumente utilizados para mostrar as categorias de topo dos tipos de espera:

- Utilize a [Consulta Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) para encontrar estatísticas de espera para cada consulta ao longo do tempo. Na Loja de Consultas, os tipos de espera são combinados em categorias de espera. Você pode encontrar o mapeamento de categorias de espera para esperar tipos em [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- Utilize [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) para devolver informações sobre todas as esperas encontradas por fios que foram executados durante o funcionamento. Pode utilizar esta vista agregada para diagnosticar problemas de desempenho com base de dados Azure SQL e também com consultas e lotes específicos.
- Utilize [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) para devolver informações sobre a fila de tarefas que estão à espera de algum recurso.

Em cenários de alta CPU, a Consulta Store e as estatísticas de espera podem não refletir o uso do CPU se:

- As consultas de alta consumista da CPU ainda estão a ser executadas.
- As altas consultas consumistas estavam a decorrer quando aconteceu uma falha.

DMVs que rastreiam A Query Store e as estatísticas de espera mostram resultados apenas com sucesso e consultas cronometradas. Não mostram dados para executar declarações até que as declarações terminem. Utilize a visão dinâmica de gestão [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) para acompanhar as consultas atualmente executadas e o tempo de trabalho associado.

O gráfico perto do início deste artigo mostra que as esperas mais comuns são:

- Fechaduras (bloqueio)
- I/O
- Contenção relacionada com tempDB
- Subsídio de memória espera

> [!IMPORTANT]
> Para um conjunto de consultas T-SQL que usam DMVs para resolver problemas relacionados com a espera, consulte:
>
> - [Identificar problemas de desempenho em I/O](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identificar esperas de concessão de memória](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)
> - [TigerToolbox espera e trava](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [usp_whatsup tigerToolbox](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="improve-database-performance-with-more-resources"></a>Melhorar o desempenho da base de dados com mais recursos

Se não forem itens atol suficientes, pode alterar a quantidade de recursos disponíveis na Base de Dados Azure SQL. Atribuir mais recursos alterando o nível de [serviço DTU](sql-database-service-tiers-dtu.md) de uma única base de dados. Ou aumentar os EDTUs de uma piscina elástica a qualquer momento. Em alternativa, se estiver a utilizar o modelo de [compra baseado em vCore,](sql-database-service-tiers-vcore.md)altere o nível de serviço ou aumente os recursos atribuídos à sua base de dados.

Para bases de dados únicas, pode alterar os níveis de [serviço ou calcular recursos](sql-database-single-database-scale.md) a pedido para melhorar o desempenho da base de dados. Para várias bases de dados, considere usar [piscinas elásticas](sql-database-elastic-pool-guidance.md) para escalar os recursos automaticamente.

## <a name="tune-and-refactor-application-or-database-code"></a>Aplicação de sintonização e refactor ou código de base de dados

Pode otimizar o código de aplicação para a base de dados, alterar índices, forçar planos ou utilizar sugestões para adaptar manualmente a base de dados à sua carga de trabalho. Para obter informações sobre a sintonização manual e reescrever o código, consulte a orientação de [afinação](sql-database-performance-guidance.md)do desempenho .

## <a name="next-steps"></a>Passos seguintes

- Para ativar a sintonização automática na Base de Dados SQL do Azure e deixar a função de afinação automática gerir totalmente a sua carga de trabalho, consulte [a afinação automática ativa](sql-database-automatic-tuning-enable.md).
- Para utilizar a sintonização manual, reveja as recomendações de [afinação no portal Azure](sql-database-advisor-portal.md). Aplique manualmente as recomendações que melhoram o desempenho das suas consultas.
- Altere os recursos disponíveis na sua base de dados alterando os níveis de serviço da Base de [Dados Azure SQL](sql-database-performance-guidance.md).
