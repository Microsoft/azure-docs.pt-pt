---
title: Monitorização e otimização de desempenho - base de dados SQL do Azure | Documentos da Microsoft
description: Sugestões para ajuste na base de dados do Azure SQL através de avaliação e melhoria de desempenho.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: ac87ce2198296b82ef5655d7d75443a0bd49df3c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57875157"
---
# <a name="monitoring-and-performance-tuning"></a>Ajuste de monitorização e desempenho

Base de dados SQL do Azure é um serviço de dados automaticamente geridos e flexível onde pode facilmente monitorizar a utilização, adicionar ou remover recursos (CPU, memória, e/s), localizar recomendações que podem melhorar o desempenho da base de dados ou permitir que a base de dados se adaptar a sua carga de trabalho e automaticamente a otimizar o desempenho.

## <a name="monitoring-database-performance"></a>Monitorização do desempenho de base de dados

A monitorização do desempenho de uma base de dados SQL do Azure é iniciada com a monitorização da utilização de recursos em relação ao nível de desempenho de base de dados que escolher. Base de dados SQL do Azure permite-lhe identificar oportunidades para melhorar e otimizar o desempenho de consulta sem alterar recursos ao rever [recomendações de otimização de desempenho](sql-database-advisor.md). Os índices em falta e as consultas mal otimizadas são razões comuns para um desempenho fraco da base de dados. Pode aplicar estas recomendações de otimização para melhorar o desempenho da carga de trabalho. Também pode permitir que banco de dados SQL do Azure para [automaticamente otimizar o desempenho das suas consultas](sql-database-automatic-tuning.md) aplicando todos identificados, recomendações e verificar o que melhorar o desempenho da base de dados.

Tem as seguintes opções de monitorização e resolução de problemas de desempenho de base de dados:

- Na [portal do Azure](https://portal.azure.com), clique em **bases de dados SQL**, selecione a base de dados e, em seguida, utilize o gráfico de monitorização para procurar recursos máximas a aproximar-se. Consumo de DTU, é apresentado por predefinição. Clique em **editar** para alterar o intervalo de tempo e os valores mostrados.
- Uso [Query Performance Insight](sql-database-query-performance.md) para identificar as consultas que gastar o máximo de recursos.
- Uso [Assistente de base de dados SQL](sql-database-advisor-portal.md) para ver as recomendações para criar e remover índices, parametrização consultas e corrigir problemas de esquema.
- Uso [informações inteligentes do SQL Azure](sql-database-intelligent-insights.md) para a monitorização automática de desempenho da sua base de dados. Depois de um problema de desempenho é detetado, é gerado um registo de diagnóstico com detalhes e análise de causa raiz (RCA) do problema. Recomendação de melhoria do desempenho é fornecida sempre que possível.
- [Ativar o ajuste automático](sql-database-automatic-tuning-enable.md) e permita que o SQL do Azure, base de dados automaticamente problemas de desempenho de correção identificada.
- Uso [vistas de gestão dinâmica (DMVs)](sql-database-monitoring-with-dmvs.md), [eventos expandidos](sql-database-xevent-db-diff-from-svr.md)e o [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) para obter mais dos problemas de desempenho de resolução de problemas.

> [!TIP]
> Ver [orientação de desempenho](sql-database-performance-guidance.md) para descobrir técnicas que pode utilizar para melhorar o desempenho da base de dados do Azure SQL depois de identificar o problema de desempenho com um ou mais dos métodos acima.

## <a name="monitor-databases-using-the-azure-portal"></a>Monitorizar bases de dados com o portal do Azure

Na [portal do Azure](https://portal.azure.com/), pode monitorizar a uma utilização de base de dados individual s selecionando a base de dados e clicando no **monitorização** gráfico. É apresentada a janela **Métricas** que pode alterar ao clicar no botão **Editar gráfico**. Adicione as métricas seguintes:

- Percentagem de CPU
- Percentagem de DTU
- Percentagem de ES de Dados
- Percentagem de tamanho da Base de Dados

Depois de adicionar estas métricas, pode continuar a visualizá-los no **monitorização** do gráfico com mais informações sobre o **métrica** janela. As quatro métricas mostram a percentagem de utilização média relativa à **DTU** da base de dados. Consulte a [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) e [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md) artigos para obter mais informações sobre escalões de serviço.  

![Monitorização da camada de serviços do desempenho da base de dados.](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

Também pode configurar alertas para as métricas de desempenho. Clique no botão **Adicionar alerta** na janela **Métricas**. Siga o assistente para configurar o alerta. Tem a opção de alertar se as métricas excederem um determinado limiar ou se a métrica descer abaixo de um determinado limiar.

Por exemplo, se espera que a carga de trabalho na sua base de dados aumente, pode optar por configurar um alerta por e-mail sempre que a base de dados atingir 80% em qualquer uma das métricas de desempenho. Pode usar isso como um aviso inicial para calcular quando poderá ter de mudar para o próximo tamanho de computação mais elevado.

As métricas de desempenho também podem ajudar a determinar se é possível mudar para um tamanho de computação mais baixo. Parta do princípio de que está a utilizar uma base de dados Standard S2 e que todas as métricas de desempenho mostram que, em média, a base de dados não utiliza mais de 10% em qualquer momento. É provável que a base de dados funcione corretamente no Standard S1. No entanto, tenha em atenção de cargas de trabalho que aumentam ou flutuam, antes de tomar a decisão de migrar para um tamanho de computação mais baixo.

## <a name="troubleshoot-performance-issues"></a>Resolver problemas de desempenho

Para diagnosticar e resolver problemas de desempenho, comece por compreender o estado de cada consulta do Active Directory e as condições que causam problemas de desempenho relevantes para o estado de cada carga de trabalho. Para melhorar o desempenho da base de dados do Azure SQL, compreenda a que cada pedido de consulta do Active Directory da sua aplicação está num Estado de espera ou de uma execução. Ao solucionar um problema de desempenho na base de dados do Azure SQL, tenha o gráfico a seguir em mente enquanto for lendo este artigo para diagnosticar e resolver problemas de desempenho.

![Estados de carga de trabalho](./media/sql-database-monitor-tune-overview/workload-states.png)

Para uma carga de trabalho com problemas de desempenho, o problema de desempenho pode ser devido a contenção de CPU (um **relacionados com a execução** condição) ou consultas individuais estão a aguardar em algo (um **relacionados com a espera** condição ).

## <a name="running-related-performance-issues"></a>Problemas de desempenho relacionados com a execução

Como orientação geral, se a sua utilização da CPU for consistentemente igual ou superior a 80%, tem um problema de desempenho relacionados com a execução. Se tiver um problema relacionado com a execução, ele pode ser causado por insuficiência de recursos de CPU ou podem estar relacionado a uma das seguintes condições:

- Demasiadas consultas em execução
- Demasiadas consultas compiling
- Um ou mais consultas em execução estiver a utilizar um plano de consulta abaixo do ideal

Se determinar que tem um problema de desempenho relacionados com a execução, o seu objetivo é identificar o problema exato a utilizar um ou mais métodos. Os métodos mais comuns para identificar problemas relacionados com a execução são:

- Utilize o [portal do Azure](#monitor-databases-using-the-azure-portal) para monitorizar a utilização de percentagem de CPU.
- Utilize o seguinte procedimento [exibições de gerenciamento dinâmico](sql-database-monitoring-with-dmvs.md):

  - [dm db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) devolve o consumo de CPU, IO e memória para uma base de dados de base de dados do Azure SQL. Não existe uma linha para cada 15 segundos, mesmo que não existe nenhuma atividade na base de dados. Dados do histórico são mantidos durante uma hora.
  - [resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) retorna dados de armazenamento e utilização de CPU para uma base de dados do SQL do Azure. Os dados são recolhidos e agregados em intervalos de cinco minutos.

> [!IMPORTANT]
> Para um conjunto um consultas de T-SQL com esses DMVs para resolver problemas de utilização de CPU, consulte [problemas de desempenho de CPU identificar](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues).

### <a name="troubleshoot-queries-with-parameter-sensitive-query-execution-plan-issues"></a>Resolver problemas de consultas com problemas de plano de execução de consulta de parâmetro sensíveis

O problema do parâmetro plano confidenciais (PSP) refere-se a um cenário em que o otimizador de consultas gera um plano de execução de consulta que é o ideal apenas para um valor de parâmetro específico (ou conjunto de valores) e o plano em cache, em seguida, é não ideal para os valores de parâmetro utilizados no execuções consecutivas. Planos de não-ideal, em seguida, podem resultar em problemas de desempenho de consulta e degradação de débito de carga de trabalho geral.

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

- Isso [sente um cheiro um parâmetro](https://blogs.msdn.microsoft.com/queryoptteam/20../../i-smell-a-parameter/) mensagem de blogue
- Isso [parâmetro, a detecção de problemas e soluções alternativas](https://blogs.msdn.microsoft.com/turgays/20../../parameter-sniffing-problem-and-possible-workarounds/) mensagem de blogue
- Isso [elefante e detecção de parâmetro de mouse](https://www.brentozar.com/archive/2013/06/the-elephant-and-the-mouse-or-parameter-sniffing-in-sql-server/) mensagem de blogue
- Isso [sql dinâmico em comparação com a qualidade de plano em consultas parametrizadas](https://blogs.msdn.microsoft.com/conor_cunningham_msft/20../../conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/) mensagem de blogue

### <a name="troubleshooting-compile-activity-due-to-improper-parameterization"></a>Resolução de problemas de atividade de compilação devido a parametrização imprópria

Quando uma consulta tem literais, o motor de base de dados escolhe automaticamente parametrizar a instrução ou um utilizador pode parametrizar explicitamente-lo para reduzir o número de compila. Um número elevado de compila de uma consulta usando o mesmo padrão, diferentes valores literais, mas pode resultar em alta utilização da CPU. Da mesma forma, se apenas parcialmente parametrizar uma consulta que continua a ter os literais, o motor de base de dados não parametrize-lo ainda mais.  Segue-se um exemplo de uma consulta parametrizada parcialmente:

```sql
select * from t1 join t2 on t1.c1=t2.c1
where t1.c1=@p1 and t2.c2='961C3970-0E54-4E8E-82B6-5545BE897F8F'
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

Consulta Store e vistas de gestão de dinâmica de controlo de estatísticas de espera apenas mostram os resultados das consultas e concluídas excedeu o tempo e não mostrar dados para a executar atualmente instruções (até que eles concluírem).  A vista de gestão dinâmica [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) permite-lhe controlar as consultas em execução e o tempo de trabalho associados.

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
