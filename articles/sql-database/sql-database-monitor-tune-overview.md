---
title: Monitorização e otimização de desempenho - base de dados SQL do Azure | Documentos da Microsoft
description: Sugestões para ajuste na base de dados do Azure SQL através de avaliação e melhoria de desempenho.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 2fa43fcd48736a3d044deb07ed690af580c3b987
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416286"
---
# <a name="monitoring-and-performance-tuning"></a>Ajuste de monitorização e desempenho

Ativa de base de dados SQL do Azure para monitorizar facilmente a utilização, adicionar ou remover recursos (CPU, memória, e/s), os potenciais problemas de resolução de problemas e encontre as recomendações que podem melhorar o desempenho da base de dados. Base de dados SQL do Azure tem muitos recursos que podem corrigir automaticamente os problemas em seus bancos de dados se deseja permitir que a base de dados se adaptar a sua carga de trabalho e otimizar automaticamente o desempenho. No entanto, existem alguns problemas personalizados, que poderá ter de resolver problemas. Este artigo explica algumas melhores práticas e ferramentas que pode utilizar para resolver os problemas de desempenho.

Existem duas atividades principais que precisa fazer para garantir que essa base de dados está a ser executado sem problemas:
- [Monitorização do desempenho de base de dados](#monitoring-database-performance) para certificar-se de que os recursos atribuídos a sua base de dados podem lidar com a sua carga de trabalho. Se vir que está a atingir os limites de recursos, precisaria para identificar recursos principais consultas de consumo e otimizá-los ou para adicionar mais recursos ao atualizar o escalão de serviço.
- [Resolver problemas de desempenho](#troubleshoot-performance-issues) para identificar o motivo pelo qual ocorreu algum problema potencial, identificar a causa raiz do problema e a ação que irá corrigir o problema.

## <a name="monitoring-database-performance"></a>Monitorização do desempenho de base de dados

A monitorização do desempenho de uma base de dados SQL do Azure é iniciada com a monitorização da utilização de recursos em relação ao nível de desempenho de base de dados que escolher. Tem de monitorizar os seguintes recursos:
 - **Utilização da CPU** -precisa verificar são atingir 100% de utilização da CPU num período mais longo do tempo. Isto pode indicar que poderá ter de atualizar a base de dados ou de instância ou identificar e ajustar as consultas que estão a utilizar a maioria do poder de computação.
 - **Estatísticas de espera** -precisa verificar o que por isso que as suas consultas estão a aguardar alguns recursos. Queriesmig esperar que os dados a ser obtido ou guardada para os ficheiros de base de dados, aguardando por algum limite de recursos é alcançado, etc.
 - **Utilização de e/s** -precisa verificar a Contatando os limites de e/s de armazenamento subjacente.
 - **Utilização da memória** -a quantidade de memória disponível em sua base de dados ou a instância é proporcional ao número de vCores e precisa verificar é o suficiente para a carga de trabalho. Expectativa de vida da página é um dos parâmetros que podem indicar são suas páginas rapidamente removidas da memória.

Base de dados SQL do Azure **fornece os avisos que podem ajudar a solucionar problemas e corrigir possíveis problemas de desempenho**. Pode determinar facilmente oportunidades para melhorar e otimizar o desempenho de consulta sem alterar recursos ao rever [recomendações de otimização de desempenho](sql-database-advisor.md). Os índices em falta e as consultas mal otimizadas são razões comuns para um desempenho fraco da base de dados. Pode aplicar estas recomendações de otimização para melhorar o desempenho da carga de trabalho. Também pode permitir que banco de dados SQL do Azure para [automaticamente otimizar o desempenho das suas consultas](sql-database-automatic-tuning.md) aplicando todos identificados, recomendações e verificar o que melhorar o desempenho da base de dados.

Tem as seguintes opções de monitorização e resolução de problemas de desempenho de base de dados:

- Na [portal do Azure](https://portal.azure.com), clique em **bases de dados SQL**, selecione a base de dados e, em seguida, utilize o gráfico de monitorização para procurar recursos máximas a aproximar-se. Consumo de DTU, é apresentado por predefinição. Clique em **editar** para alterar o intervalo de tempo e os valores mostrados.
- Ferramentas como SQL Server Management Studio fornecem vários relatórios úteis, como um [Dashboard de desempenho](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard?view=sql-server-2017) onde pode monitorizar a utilização de recursos e identificar os principais recursos que consumir consultas, ou [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed)onde pode identificar as consultas com desempenho regredido.
- Uso [Query Performance Insight](sql-database-query-performance.md) a [portal do Azure](https://portal.azure.com) para identificar as consultas que gastar o máximo de recursos. Esta funcionalidade só está disponível na base de dados e conjuntos elásticos.
- Uso [Assistente de base de dados SQL](sql-database-advisor-portal.md) para ver as recomendações para criar e remover índices, parametrização consultas e corrigir problemas de esquema. Esta funcionalidade só está disponível na base de dados e conjuntos elásticos.
- Uso [informações inteligentes do SQL Azure](sql-database-intelligent-insights.md) para a monitorização automática de desempenho da sua base de dados. Depois de um problema de desempenho é detetado, é gerado um registo de diagnóstico com detalhes e análise de causa raiz (RCA) do problema. Recomendação de melhoria do desempenho é fornecida sempre que possível.
- [Ativar o ajuste automático](sql-database-automatic-tuning-enable.md) e permita que o SQL do Azure, base de dados automaticamente problemas de desempenho de correção identificada.
- Uso [vistas de gestão dinâmica (DMVs)](sql-database-monitoring-with-dmvs.md), [eventos expandidos](sql-database-xevent-db-diff-from-svr.md)e o [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) para obter mais dos problemas de desempenho de resolução de problemas.

> [!TIP]
> Ver [orientação de desempenho](sql-database-performance-guidance.md) para descobrir técnicas que pode utilizar para melhorar o desempenho da base de dados do Azure SQL depois de identificar o problema de desempenho com um ou mais dos métodos acima.

## <a name="troubleshoot-performance-issues"></a>Resolver problemas de desempenho

Para diagnosticar e resolver problemas de desempenho, comece por compreender o estado de cada consulta do Active Directory e as condições que causam problemas de desempenho relevantes para o estado de cada carga de trabalho. Para melhorar o desempenho da base de dados do Azure SQL, compreenda a que cada pedido de consulta do Active Directory da sua aplicação está num Estado de espera ou de uma execução. Ao solucionar um problema de desempenho na base de dados do Azure SQL, tenha o gráfico a seguir em mente enquanto for lendo este artigo para diagnosticar e resolver problemas de desempenho.

![Estados de carga de trabalho](./media/sql-database-monitor-tune-overview/workload-states.png)

Para uma carga de trabalho com problemas de desempenho, o problema de desempenho pode ser devido a contenção de CPU (um **relacionados com a execução** condição) ou consultas individuais estão a aguardar em algo (um **relacionados com a espera** condição ).

As causas ou **relacionados com a execução** problemas podem ser:
- **Problemas de compilação** -otimizador de consultas SQL poderá produzir um plano abaixo do ideal devido a estatísticas obsoletas, incorreta estimativa do número de linhas que serão processados ou a estimativa de memória necessária. Se sabe que essa consulta foi executada com mais rapidez no passado ou em outra instância (instância de instância gerida ou do SQL Server), tire os planos de execução real e compare-lhes para verem são diferentes. Experimente a aplicar as sugestões de consulta ou recompilações estatísticas ou índices para obter o melhor plano. Ative a correção de planos automática na base de dados do Azure SQL automaticamente reduzir esses problemas.
- **Problemas de execução** - se o plano de consulta é o ideal, em seguida, ele provavelmente está atingindo alguns limites de recursos na base de dados, como o débito de escrita de registo ou usar desfragmentados índices que devem ser reconstruídos. Um grande número de consultas em simultâneo que são gastos os recursos também pode ser a causa dos problemas de execução. **Relacionados com a espera** problemas estão na maioria dos casos relacionados com os problemas de execução, uma vez que as consultas que não estão a executar com eficiência provavelmente estão a aguardar alguns recursos.

As causas ou **relacionados com a espera** problemas podem ser:
- **Bloquear** -uma consulta poderão conter o bloqueio em alguns objetos na base de dados, enquanto outros estão a tentar aceder aos mesmos objetos. Pode identificar facilmente as consultas de bloqueios usando a DMV ou ferramentas de monitorização.
- **Problemas de e/s** -consultas poderão ser aguardar que as páginas de escrita de dados ou ficheiros de registo. Neste caso, verá `INSTANCE_LOG_RATE_GOVERNOR`, `WRITE_LOG`, ou `PAGEIOLATCH_*` estatísticas no DMV de espera.
- **Problemas de TempDB** – se estiver a utilizar muitas tabelas temporárias ou verá muita TempDB spills nos seus planos de suas consultas pode ter um problema com o débito de TempDB. 
- **Problemas relacionados com a memória** -poderá não ter memória suficiente para a sua carga de trabalho para que a expectativa de vida da página podem ignorar ou as suas consultas estão a obter menos concessão de memória que o necessário. Em alguns casos, a inteligência incorporada no otimizador de consulta irá corrigir estes problemas.
 
Nas seções a seguir será explicado como identificar e resolver problemas de alguns desses problemas.

## <a name="running-related-performance-issues"></a>Problemas de desempenho relacionados com a execução

Como orientação geral, se a sua utilização da CPU for consistentemente igual ou superior a 80%, tem um problema de desempenho relacionados com a execução. Se tiver um problema relacionado com a execução, ele pode ser causado por insuficiência de recursos de CPU ou podem estar relacionado a uma das seguintes condições:

- Demasiadas consultas em execução
- Demasiadas consultas compiling
- Um ou mais consultas em execução estiver a utilizar um plano de consulta abaixo do ideal

Se determinar que tem um problema de desempenho relacionados com a execução, o seu objetivo é identificar o problema exato a utilizar um ou mais métodos. Os métodos mais comuns para identificar problemas relacionados com a execução são:

- Utilize o [portal do Azure](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal) para monitorizar a utilização de percentagem de CPU.
- Utilize o seguinte procedimento [exibições de gerenciamento dinâmico](sql-database-monitoring-with-dmvs.md):

  - [dm db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) devolve o consumo de CPU, IO e memória para uma base de dados de base de dados do Azure SQL. Não existe uma linha para cada 15 segundos, mesmo que não existe nenhuma atividade na base de dados. Dados do histórico são mantidos durante uma hora.
  - [resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) retorna dados de armazenamento e utilização de CPU para uma base de dados do SQL do Azure. Os dados são recolhidos e agregados em intervalos de cinco minutos.

> [!IMPORTANT]
> Para um conjunto um consultas de T-SQL com esses DMVs para resolver problemas de utilização de CPU, consulte [problemas de desempenho de CPU identificar](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues).

### <a name="ParamSniffing"></a> Resolver problemas de consultas com problemas de plano de execução de consulta de parâmetro sensíveis

O problema do parâmetro plano confidenciais (PSP) refere-se a um cenário em que o otimizador de consultas gera um plano de execução de consulta que é o ideal apenas para um valor de parâmetro específico (ou conjunto de valores) e o plano em cache, em seguida, é não ideal para os valores de parâmetro utilizados no execuções consecutivas. Planos de não-ideal, em seguida, podem resultar em problemas de desempenho de consulta e degradação de débito de carga de trabalho geral. Para obter mais informações sobre a detecção de parâmetro e o processamento de consultas, consulte a [guia de arquitetura de processamento de consulta](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

Existem várias soluções alternativas seguidas para atenuar problemas, cada qual com vantagens e desvantagens associadas e as desvantagens:

- Utilize o [RECOMPILAR](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) sugestão de consulta em cada execução de consulta. Esta solução negocia tempo de compilação e CPU maior para melhor qualidade de plano. Usando o `RECOMPILE` opção geralmente não é possível para cargas de trabalho que requerem um débito elevado.
- Utilize o [opção (OTIMIZAR para....) ](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) sugestão de consulta para substituir o valor do parâmetro real com um valor de parâmetro típico que produz um plano de bom o suficiente para a maioria das possibilidades de valor de parâmetro.   Esta opção requer uma boa compreensão dos valores de parâmetro ideal e características de plano associados.
- Uso [opção (OTIMIZAR para desconhecido)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) sugestão de consulta para substituir o valor do parâmetro real em troca usando a média de vetor de densidade. Outra forma de fazer isso é capturar os valores de parâmetro de entrada em variáveis locais e, em seguida, usando as variáveis locais dentro os predicados em vez de utilizar os parâmetros propriamente ditos. A densidade média tem de ser *bom o suficiente* com esta correção específica.
- Desativar a detecção de parâmetro utilizando inteiramente os [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) sugestão de consulta.
- Utilize o [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) sugestão de consulta para evitar as recompilações enquanto estiver no cache. Esta solução assume que o *bom enough* plano comuns já está em cache. Também pode desativar as atualizações automáticas para estatísticas para reduzir a possibilidade de bom plano a ser expulso e um novo plano ruim que estão sendo compilados.
- Forçar o plano usando explicitamente [USE PLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) sugestão de consulta (ao especificar explicitamente, definindo um plano específico usando a consulta Store ou Ativando [otimização automática](sql-database-automatic-tuning.md).
- Substitua o procedimento único com um conjunto aninhado de procedimentos que pode cada ser utilizado com base na lógica condicional e os valores dos parâmetros associados.
- Crie a seqüência de caracteres dinâmica alternativas de execução para uma definição de procedimento estático.

Para obter mais informações sobre como resolver estes tipos de problemas, consulte:

- Isso [Faro, um parâmetro](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/) mensagem de blogue
- Isso [sql dinâmico em comparação com a qualidade de plano em consultas parametrizadas](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/) mensagem de blogue
- Isso [técnicas de otimização de consulta SQL no SQL Server: Parâmetro Sniffing](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/) mensagem de blogue

### <a name="troubleshooting-compile-activity-due-to-improper-parameterization"></a>Resolução de problemas de atividade de compilação devido a parametrização imprópria

Quando uma consulta tem literais, o motor de base de dados escolhe automaticamente parametrizar a instrução ou um utilizador pode parametrizar explicitamente-lo para reduzir o número de compila. Um número elevado de compila de uma consulta usando o mesmo padrão, diferentes valores literais, mas pode resultar em alta utilização da CPU. Da mesma forma, se apenas parcialmente parametrizar uma consulta que continua a ter os literais, o motor de base de dados não parametrize-lo ainda mais.  Segue-se um exemplo de uma consulta parametrizada parcialmente:

```sql
SELECT * FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

No exemplo anterior, `t1.c1` demora `@p1` mas `t2.c2` continua tirar GUID como literal. Neste caso, se alterar o valor para `c2`, a consulta será tratada como uma consulta diferente e uma nova compilação ocorrerá. Para reduzir as compilações no exemplo anterior, a solução é parametrizar também o GUID.

A seguinte consulta mostra a contagem de consultas por hash de consulta para determinar se uma consulta corretamente é parametrizada ou não:

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
### <a name="factors-influencing-query-plan-changes"></a>Fatores influenciar as alterações de plano de consulta

A recompilação de plano de execução de consulta pode resultar num plano de consulta gerada, que é diferente do que originalmente foi colocado em cache. Existem vários motivos por que um plano original existente pode ser recompilado automaticamente:
- Alterações no esquema a ser referenciado pela consulta
- Alterações de dados para as tabelas a ser referenciadas pela consulta 
- Alterações às opções de contexto de consulta 

Um plano compilado pode ser desconsiderado da cache para uma variedade de motivos, incluindo os reinícios de instância, alterações de configuração, pressão de memória e solicitações explícitas para limpar a cache de âmbito de base de dados. Além disso, o uso de uma sugestão de RECOMPILE significa que um plano de não ser colocado em cache.

Uma recompilação (ou nova compilação depois da expulsão de cache), ainda pode resultar na geração de um plano de execução de consulta idêntica a partir de um originalmente observado.  Se, no entanto, existem alterações ao plano em comparação comparada o plano anterior ou original, seguem-se a mais comuns explicações sobre o motivo pelo qual um plano de execução de consultas foi alterado:

- **Alterar o design físico**. Por exemplo, o novo índice criado que com mais eficiência abrangem que os requisitos de uma consulta podem ser utilizados numa nova compilação se o otimizador de consultas decidir é mais ideal para aproveitar esse novo índice que utilizar a estrutura de dados selecionada originalmente para a primeira versão do a execução da consulta.  Quaisquer alterações de físicas para os objetos referenciados podem resultar numa nova opção de plano em tempo de compilação.

- **Diferenças de recursos de servidor**. Num cenário em que um único plano é diferente no "sistema" versus "sistema B" – a disponibilidade de recursos, como o número de processadores disponíveis, pode influenciar o plano será gerado.  Por exemplo, se um sistema tem um número mais elevado de processadores, um plano paralelo poderá ser escolhido. 

- **Estatísticas de diferentes**. As estatísticas de associadas com os objetos referenciados alterado ou materialmente diferentes de estatísticas do sistema original.  Se alterar as estatísticas e ocorre uma recompilação, o otimizador de consultas será estatísticas de utilização a partir desse ponto específico no tempo. As estatísticas revisadas poderão ter distribuições de dados significativamente diferentes e frequências que não eram o caso em que a compilação original.  Estas alterações são utilizadas para calcular as estimativas de cardinalidade (número de linhas antecipada para fluir através da árvore de lógica de consulta).  As alterações para as estimativas de cardinalidade podem levar-nos para escolher diferentes operadores físicos e associadas ordem de operações.  Até mesmo pequenas mudanças para estatísticas podem resultar num plano de execução de consulta alterada.

- **Base de dados alterados compatibilidade nível ou cardinalidade estimator versão**.  As alterações para o nível de compatibilidade de base de dados podem ativar novas estratégias e funcionalidades que podem resultar num plano de execução de consulta diferentes.  Além do nível de compatibilidade de base de dados, desativar ou ativar o sinalizador de rastreio 4199 ou alterar o estado da configuração de âmbito da base de dados também podem influenciar o QUERY_OPTIMIZER_HOTFIXES consultar opções do plano de execução no momento da compilação.  Sinalizadores de rastreio 9481 (força herdados CE) e 2312 (forçar CE padrão) são também planear afetar. 

### <a name="resolve-problem-queries-or-provide-more-resources"></a>Resolver a consultas de problemas ou fornecer mais recursos

Depois de identificar o problema, pode ajustar as consultas de problema ou atualizar o tamanho de computação ou camada para aumentar a capacidade da base de dados SQL do Azure que visam absorver os requisitos de CPU de serviço. Para obter informações sobre como dimensionar recursos de bases de dados individuais, consulte [Dimensionar recursos de base de dados individual no Azure SQL Database](sql-database-single-database-scale.md) e, para dimensionar os recursos para conjuntos elásticos, veja [Dimensionar recursos de conjunto elástico de SQL do Azure Base de dados](sql-database-elastic-pool-scale.md). Para obter informações sobre como dimensionar uma instância gerida, veja [limites de recursos de nível de instância](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits).

### <a name="determine-if-running-issues-due-to-increase-workload-volume"></a>Determinar se problemas devido ao aumento de volume de carga de trabalho a executar

Um aumento no tráfego de aplicativo e a carga de trabalho pode contribuir para o aumento da utilização da CPU, mas precisa ter cuidado para corretamente diagnosticar este problema. Num cenário de CPU alta, responda a estas questões para determinar se, de fato, um aumento de CPU é devido a alterações de volume da carga de trabalho:

1. São as consultas da aplicação a causa do problema de CPU alta?
2. Para as consumo de CPU consultas principais (que podem ser identificadas):

   - Determine se houve vários planos de execução associados com a mesma consulta. Se assim for, determine por quê.
   - Para consultas com o mesmo plano de execução, determine se os tempos de execução eram consistentes e se a contagem de execução aumentou. Se Sim, existem problemas de desempenho provavelmente devido ao aumento de carga de trabalho.

Para resumir, se o plano de execução de consulta não foi executada de forma diferente, mas aumentou de utilização da CPU, juntamente com a contagem de execução, provavelmente, existe um problema de desempenho relacionados com o aumento de carga de trabalho.

Nem sempre é fácil concluir que existe uma alteração de volume da carga de trabalho que está orientando um problema de CPU.   Fatores a considerar: 

- **Utilização de recursos foi alterada**

  Por exemplo, considere um cenário em que o CPU aumentou para 80% durante um período prolongado de tempo.  Utilização da CPU sozinha não significa que o volume de carga de trabalho foi alterado.  Consultar o plano de execução regressões e alterações de distribuição de dados também podem contribuir para obter mais utilização de recursos, apesar da aplicação está a executar a mesma carga de trabalho exata.

- **Nova consulta apareceu**

   Um aplicativo pode levar a um novo conjunto de consultas em momentos diferentes.

- **Número de pedidos aumenta ou diminui**

   Este cenário é a medida mais óbvia da carga de trabalho. O número de consultas sempre não corresponde a mais de utilização de recursos. No entanto, esta métrica ainda é um sinal significativo, partindo do princípio de que outros fatores permanecem inalterados.

## <a name="waiting-related-performance-issues"></a>Problemas de desempenho relacionados com a espera

Assim que tenha a certeza de que não está a enfrentar uma CPU de alta, o problema de desempenho relacionados com a execução, está a enfrentar um problema de desempenho relacionados com a espera. Ou seja, seus recursos de CPU não estão sendo usados com eficiência porque está a aguardar a CPU em algum outro tipo de recurso. Neste caso, sua próxima etapa é identificar o que os recursos da CPU estão a aguardar. Categorias de tipo de espera de métodos mais comuns para mostrar parte superior:

- O [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) fornece estatísticas de espera por consulta ao longo do tempo. No Query Store, os tipos de espera são combinados em categorias de espera. O mapeamento das categorias de espera de espera tipos está disponível no [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) devolve informações sobre todas as esperas encontrados por threads que executados durante a operação. Pode utilizar esta vista agregada para diagnosticar problemas de desempenho com a base de dados do Azure SQL e também com consultas específicas e lotes.
- [os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) retorna informações sobre a fila de espera de tarefas que estão a aguardar em algum recurso.

Em cenários de CPU alta, a consulta Store e as estatísticas de espera nem sempre reflete utilização da CPU por estes dois motivos:

- Consultas de consumo de CPU alta podem ainda ser em execução e as consultas ainda não terminou
- As consultas de consumo de CPU alta estavam em execução aquando da ocorrência de uma ativação pós-falha

Consulta Store e vistas de gestão de dinâmica de controlo de estatísticas de espera apenas mostram os resultados das consultas e concluídas excedeu o tempo e não mostrar dados para a executar atualmente instruções (até que eles concluírem). A vista de gestão dinâmica [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) permite-lhe controlar as consultas em execução e o tempo de trabalho associados.

Conforme mostrado no gráfico anterior, as esperas mais comuns são:

- Bloqueios (bloqueio)
- I/O
- `tempdb`-relacionados com a contenção
- Esperas de concessão de memória

> [!IMPORTANT]
> Para um conjunto um consultas de T-SQL com esses DMVs para resolver estes problemas relacionados com a espera, consulte:
>
> - [Identificar problemas de desempenho de e/s](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identificar `tempdb` problemas de desempenho](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identificar as esperas de concessão de memória](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)
> - [TigerToolbox - deve aguardar e Travas](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox - usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="improving-database-performance-with-more-resources"></a>Melhorando o desempenho de base de dados com mais recursos

Por fim, se não existirem não existem itens passíveis de ação que podem melhorar o desempenho da base de dados, pode alterar a quantidade de recursos disponíveis no banco de dados do Azure SQL. Pode atribuir mais recursos ao alterar o [escalão de serviço DTU](sql-database-service-tiers-dtu.md) de um único banco de dados ou aumentar as eDTUs de um conjunto elástico em qualquer altura. Em alternativa, se estiver a utilizar o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md), pode alterar a camada de serviço ou aumentar os recursos alocados à base de dados.

1. Para obter bases de dados individuais, pode [alterar escalões de serviço](sql-database-single-database-scale.md) ou [recursos de computação](sql-database-single-database-scale.md) sob demanda para melhorar o desempenho da base de dados.
2. Para várias bases de dados, considere a utilização [conjuntos elásticos](sql-database-elastic-pool-guidance.md) Dimensionar recursos automaticamente.

## <a name="tune-and-refactor-application-or-database-code"></a>Otimizar e um aplicativo do refactor ou código de base de dados

Pode alterar o código do aplicativo de maneira ideal, utilizar a base de dados, alterar os índices, forçar planos ou utilizar sugestões para adaptar manualmente a base de dados para a sua carga de trabalho. Encontrar algumas orientações e sugestões para ajuste manual e reescrever o código na [tópico de documentação de orientação de desempenho](sql-database-performance-guidance.md) artigo.

## <a name="next-steps"></a>Passos Seguintes

- Para ativar a otimização automática na base de dados do Azure SQL e permitir que a funcionalidade de otimização automática gerenciar totalmente a sua carga de trabalho, consulte [ativar o ajuste automático](sql-database-automatic-tuning-enable.md).
- Para utilizar a ajuste manual, pode rever [recomendações no portal do Azure de otimização](sql-database-advisor-portal.md) e aplicar manualmente os que melhorar o desempenho das suas consultas.
- Alterar os recursos que estão disponíveis na sua base de dados ao alterar [escalões de serviço de base de dados do Azure SQL](sql-database-performance-guidance.md)
