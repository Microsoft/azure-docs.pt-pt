---
title: Parâmetros do servidor - Hiperescala (Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Parâmetros na Hiperescala (Citus) SQL API
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 57258540f3cd7b4c47b662b0885453cedd188e5b
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136964"
---
# <a name="server-parameters"></a>Parâmetros do servidor

Existem vários parâmetros do servidor que afetam o comportamento da Hiperescala (Citus), ambos parâmetros do PostgreSQL padrão, e parâmetros específicos da Hiperescala (Citus). Para saber mais sobre os parâmetros de configuração postgreSQL, pode visitar a secção de configuração do tempo de [execução](http://www.postgresql.org/docs/current/static/runtime-config.html) da documentação PostgreSQL.

O resto desta referência visa discutir parâmetros específicos de configuração de Hiperescala (Citus). Estes parâmetros podem ser definidos no portal Azure sob **parâmetros do nó do trabalhador** em **Definições** para um grupo de servidor de Hiperescala (Citus).

> [!NOTE]
>
> Os grupos de servidores de hiperescala que executam versões mais antigas do Citus Engine podem não oferecer todos os parâmetros listados abaixo.

## <a name="general-configuration"></a>Configuração geral

### <a name="citususe_secondary_nodes-enum"></a>citus.use \_ \_ nós secundários (enum)

Define a política a utilizar ao escolher nós para consultas SELECT. Se estiver definido para "sempre", então o planejador perguntará apenas nós que são marcados como noderole "secundário" em [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table).

Os valores suportados para este enum são:

-   **nunca:** (padrão) Todas as leituras acontecem nos nós primários.
-   **sempre:** As leituras são executadas contra nós secundários e as declarações de inserção/atualização são desativadas.

### <a name="cituscluster_name-text"></a>nome citus.cluster \_ (texto)

Informa o coordenador do nós que coordena o agrupamento. Uma vez \_ definido o nome do cluster, o planejador irá consultar os nós dos trabalhadores apenas nesse cluster.

### <a name="citusenable_version_checks-boolean"></a>citus.enable \_ \_ version checks (boolean)

A atualização da versão Hyperscale (Citus) requer um reinício do servidor (para recolher a nova biblioteca partilhada), seguido do comando ALTER EXTENSION UPDATE.  A não execução de ambos os passos pode potencialmente causar erros ou acidentes.
A hiperescala (Citus) valida assim a versão do código e a da correspondência de extensão, e falha se não o \' fizerem.

Este valor não é verdadeiro e é eficaz no coordenador. Em casos raros, processos de atualização complexos podem exigir a definição deste parâmetro como falso, desativando assim a verificação.

### <a name="cituslog_distributed_deadlock_detection-boolean"></a>citus.log \_ distribuído \_ \_ deteção de impasse (boolean)

Se registar o processamento distribuído de deteção de impasse no registo do servidor. Não é falso.

### <a name="citusdistributed_deadlock_detection_factor-floating-point"></a>citus.distributed \_ fator de deteção de impasse \_ \_ (ponto flutuante)

Define o tempo para esperar antes de verificar se há impasses distribuídos. Em particular, o tempo de espera será este valor multiplicado pela \' definição de [tempo de \_ tempo](https://www.postgresql.org/docs/current/static/runtime-config-locks.html) de impasse postgreSQL. O valor predefinido é `2`. Um valor de `-1` desativação distribuído deteção de impasse.

### <a name="citusnode_connection_timeout-integer"></a>tempo limite de ligação citus.node \_ \_ (inteiro)

O `citus.node_connection_timeout` GUC define a duração máxima (em milissegundos) para aguardar o estabelecimento de ligação. A hiperescala (Citus) levanta um erro se o tempo limite decorrer antes de ser estabelecida pelo menos uma ligação de trabalhador. Este GUC afeta as ligações do coordenador aos trabalhadores e dos trabalhadores uns com os outros.

-   Predefinição: cinco segundos
-   Mínimo: 10 milissegundos
-   Máximo: uma hora

```postgresql
-- set to 30 seconds
ALTER DATABASE foo
SET citus.node_connection_timeout = 30000;
```

## <a name="query-statistics"></a>Estatísticas de Consulta

### <a name="citusstat_statements_purge_interval-integer"></a>citus.stat \_ statements purga intervalo \_ \_ (inteiro)

Define a frequência em que o daemon de manutenção remove registos de [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table) incomparáveis em `pg_stat_statements` . Este valor de configuração define o intervalo de tempo entre purgas em segundos, com um valor predefinido de 10. Um valor de 0 desativa as purgas.

```psql
SET citus.stat_statements_purge_interval TO 5;
```

Este parâmetro é eficaz no coordenador e pode ser alterado no tempo de funcionamento.

## <a name="data-loading"></a>Carregamento de dados

### <a name="citusmulti_shard_commit_protocol-enum"></a>protocolo de \_ compromisso citus.multi-fragmentos \_ \_ (enum)

Define o protocolo de compromisso a utilizar ao executar COPY numa tabela distribuída de haxixe. Em cada colocação de fragmentos individuais, o COPY é realizado num bloco de transações para garantir que não são ingeridos dados se ocorrer um erro durante o COPY. No entanto, existe um caso particular de falha em que o COPY tem sucesso em todas as colocações, mas uma falha (hardware) ocorre antes de todas as transações se cometerem. Este parâmetro pode ser utilizado para evitar a perda de dados nesse caso, escolhendo entre os seguintes protocolos de compromisso:

-   **2pc:** (padrão) As transações em que copy é realizado nas colocações de fragmentos são preparadas primeiro usando o \' [compromisso de duas fases](http://www.postgresql.org/docs/current/static/sql-prepare-transaction.html) do PostgreSQL e depois cometidos. Os compromissos falhados podem ser recuperados manualmente ou abortados utilizando o COMMIT PREPARED ou o ROLLBACK PREPARADO, respectivamente.
    Ao utilizar 2pc, [ \_ as \_ transações preparadas max](http://www.postgresql.org/docs/current/static/runtime-config-resource.html) devem ser aumentadas em todos os trabalhadores, tipicamente com o mesmo valor que as \_ ligações máximas.
-   **1pc:** As transações em que o COPY é realizado nas colocações de fragmentos são cometidas numa única ronda. Os dados podem ser perdidos se um compromisso falhar após o COPY ter sucesso em todas as colocações (raras).

### <a name="citusshard_replication_factor-integer"></a>citus.shard \_ \_ replication fator (inteiro)

Define o fator de replicação para fragmentos que é, o número de nós em que os fragmentos serão colocados, e predefinidos para 1. Este parâmetro pode ser definido no tempo de execução e é eficaz no coordenador. O valor ideal para este parâmetro depende do tamanho do cluster e da taxa de falha do nó.  Por exemplo, pode querer aumentar este fator de replicação se executar grandes aglomerados e observar falhas de nó numa base mais frequente.

### <a name="citusshard_count-integer"></a>citus.shard \_ count (inteiro)

Define a contagem de fragmentos para tabelas de hash-partitioned e predefinições para 32.  Este valor é usado pelo [create_distributed_table](reference-hyperscale-functions.md#create_distributed_table) UDF ao criar mesas de partição de haxixe. Este parâmetro pode ser definido no tempo de execução e é eficaz no coordenador.

### <a name="citusshard_max_size-integer"></a>citus.shard \_ tamanho máximo \_ (inteiro)

Define o tamanho máximo ao qual um fragmento crescerá antes de ser dividido e predefinido para 1 GB. Quando o tamanho do ficheiro de origem \' (que é utilizado para a encenação) para um fragmento excede este valor de configuração, a base de dados garante que um novo fragmento é criado. Este parâmetro pode ser definido no tempo de execução e é eficaz no coordenador.

## <a name="planner-configuration"></a>Configuração do planejador

### <a name="cituslimit_clause_row_fetch_count-integer"></a>citus.limit \_ clause row fetch count \_ \_ \_ (inteiro)

Define o número de linhas para obter por tarefa para a otimização da cláusula de limite.
Em alguns casos, consultas selecionadas com cláusulas limite podem precisar de buscar todas as linhas de cada tarefa para gerar resultados. Nesses casos, e onde uma aproximação produziria resultados significativos, este valor de configuração define o número de linhas para obter de cada fragmento. As aproximações de limite são desativadas por predefinição e este parâmetro está definido para -1. Este valor pode ser definido no tempo de execução e é eficaz no coordenador.

### <a name="cituscount_distinct_error_rate-floating-point"></a>citus.count \_ taxa de erro distinta \_ \_ (ponto flutuante)

A hiperescala (Citus) pode calcular a contagem (distinta) aproximadas usando a extensão pós-hora-hll. Esta entrada de configuração define a taxa de erro desejada ao calcular a contagem (distinta). 0.0, que é o padrão, desativa as aproximações para a contagem (distinta); e 1.0 não fornece garantias sobre a exatidão dos resultados. Recomendamos definir este parâmetro para 0,005 para obter os melhores resultados. Este valor pode ser definido no tempo de execução e é eficaz no coordenador.

### <a name="citustask_assignment_policy-enum"></a>\_ \_ citus.task assignment policy (enum)

> [!NOTE]
> Este GUC só é aplicável quando [shard_replication_fator](reference-hyperscale-parameters.md#citusshard_replication_factor-integer) for maior do que um, ou para consultas contra [reference_tables](concepts-hyperscale-distributed-data.md#type-2-reference-tables).

Define a política a utilizar na atribuição de tarefas aos trabalhadores. O coordenador atribui tarefas aos trabalhadores com base em locais de shard. Este valor de configuração especifica a política a utilizar ao esemear estas atribuições.
Atualmente, existem três possíveis políticas de atribuição de tarefas que podem ser usadas.

-   **ganancioso:** A política gananciosa é o incumprimento e visa distribuir uniformemente tarefas pelos trabalhadores.
-   **rodada-robin:** A política de rodapé atribui tarefas aos trabalhadores de forma redonda alternando entre réplicas diferentes. Esta política permite uma melhor utilização do cluster quando a contagem de fragmentos para uma tabela é baixa em comparação com o número de trabalhadores.
-   **primeira réplica:** A política de primeira réplica atribui tarefas com base na ordem de inserção de colocações (réplicas) para os fragmentos. Por outras palavras, a consulta de fragmento para um fragmento é atribuída ao trabalhador que tem a primeira réplica desse fragmento.
    Este método permite-lhe ter fortes garantias sobre quais fragmentos serão usados em que nó (isto é, garantias de residência de memória mais fortes).

Este parâmetro pode ser definido no tempo de execução e é eficaz no coordenador.

## <a name="intermediate-data-transfer"></a>Transferência intermédia de dados

### <a name="citusbinary_worker_copy_format-boolean"></a>formato de cópia do trabalhador citus.binary \_ \_ \_ (boolean)

Utilize o formato de cópia binária para transferir dados intermédios entre os trabalhadores.
Durante a grande mesa, a Hyperscale (Citus) pode ter de repartir dinamicamente e baralhar dados entre diferentes trabalhadores. Por predefinição, estes dados são transferidos em formato de texto. O habilitar este parâmetro instrui a base de dados a utilizar o formato binário de serialização do PostgreSQL para transferir estes dados. Este parâmetro é eficaz nos trabalhadores e precisa de ser alterado no ficheiro postgresql.conf. Após a edição do ficheiro config, os utilizadores podem enviar um sinal SIGHUP ou reiniciar o servidor para que esta alteração entre em vigor.

### <a name="citusbinary_master_copy_format-boolean"></a>formato de cópia master citus.binary \_ \_ \_ (boolean)

Utilize o formato de cópia binária para transferir dados entre o coordenador e os trabalhadores. Ao executar consultas distribuídas, os trabalhadores transferem os seus resultados intermédios para o coordenador para agregação final. Por predefinição, estes dados são transferidos em formato de texto. O habilitar este parâmetro instrui a base de dados a utilizar o formato binário de serialização do PostgreSQL para transferir estes dados.
Este parâmetro pode ser definido em tempo de execução e é eficaz no coordenador.

### <a name="citusmax_intermediate_result_size-integer"></a>citus.max \_ tamanho do resultado intermédio \_ \_ (inteiro)

A dimensão máxima em KB dos resultados intermédios para os CTEs que não podem ser empurrados para baixo para os nós dos trabalhadores para a execução, e para subqueries complexos. O padrão é de 1 GB, e um valor de -1 significa que não há limite.
As consultas que excedam o limite serão canceladas e produzirão uma mensagem de erro.

## <a name="ddl"></a>DDL

### <a name="citusenable_ddl_propagation-boolean"></a>citus.permitir \_ a propagação ddl \_ (boolean)

Especifica se propagar automaticamente o DDL do coordenador para todos os trabalhadores. O valor predefinido é true. Como algumas alterações de esquema requerem um bloqueio exclusivo de acesso nas mesas, e porque a propagação automática se aplica a todos os trabalhadores sequencialmente, pode tornar um cluster de Hiperescala (Citus) temporariamente menos responsivo. Pode optar por desativar esta definição e propagar alterações manualmente.

## <a name="executor-configuration"></a>Configuração do executor

### <a name="general"></a>Geral

#### <a name="citusall_modifications_commutative"></a>citus.todas as \_ modificações \_ comutativas

A Hiperescala (Citus) impõe regras de comutação e adquire fechaduras apropriadas para modificar operações de forma a garantir a correção do comportamento. Por exemplo, assume que uma declaração INSERT viaja com outra declaração INSERT, mas não com uma declaração DE ATUALIZAÇÃO ou DELETE. Da mesma forma, assume que uma declaração DE ATUALIZAÇÃO ou DELETE não se desloca com outra declaração DE ATUALIZAÇÃO ou DELETE. Esta precaução significa que UPDATEs e DELETEs exigem que a Hyperscale (Citus) adquira fechaduras mais fortes.

Se tiver declarações DE ATUALIZAÇÃO que sejam comutativas com os seus INSERTs ou outros UPDATEs, então pode relaxar estes pressupostos de comutação definindo este parâmetro para o verdadeiro. Quando este parâmetro é definido como verdadeiro, todos os comandos são considerados comutativos e reivindicam uma fechadura partilhada, o que pode melhorar a produção geral. Este parâmetro pode ser definido em tempo de execução e é eficaz no coordenador.

#### <a name="citusremote_task_check_interval-integer"></a>citus.remote \_ task check interval \_ \_ (inteiro)

Define a frequência em que a Hyperscale (Citus) verifica os estatutos dos postos de trabalho geridos pelo executor de rastreadores de tarefas. Falha em 10 ms. O coordenador atribui tarefas aos trabalhadores e, em seguida, verifica regularmente com eles sobre cada \' trabalho de progresso. Este valor de configuração define o intervalo de tempo entre duas verificações consequentes. Este parâmetro é eficaz no coordenador e pode ser definido em tempo de execução.

#### <a name="citustask_executor_type-enum"></a>citus.task \_ executor \_ (enum)

A Hiperescala (Citus) tem três tipos de executor para executar consultas SELECT distribuídas.  O executor pretendido pode ser selecionado definindo este parâmetro de configuração. Os valores aceites para este parâmetro são:

-   **adaptativo:** O padrão. É ideal para respostas rápidas a consultas que envolvem agregações e juntas de colocated que se estendem por vários fragmentos.
-   **rastreador de tarefas:** O executor de rastreador de tarefas é adequado para consultas complexas e de longa duração que requerem baralhar dados através dos nós dos trabalhadores e uma gestão eficiente dos recursos.
-   **em tempo real:** (precotado) Serve um propósito semelhante ao executor adaptativo, mas é menos flexível e pode causar mais pressão de ligação nos nós dos trabalhadores.

Este parâmetro pode ser definido no tempo de execução e é eficaz no coordenador.

#### <a name="citusmulti_task_query_log_level-enum-multi_task_logging"></a>citus.multi \_ \_ nível de registo de consulta de tarefa \_ \_ (enum) {#multi_task_logging}

Define um nível de log para qualquer consulta que gere mais do que uma tarefa (isto é, que atinge mais de um fragmento). O registo madeireira é útil durante uma migração de aplicações multi-arrendatários, pois pode optar por erro ou alertar para tais consultas, para encontrá-las e \_ adicionar-lhes um filtro de identificação de inquilino. Este parâmetro pode ser definido em tempo de execução e é eficaz no coordenador. O valor predefinido para este parâmetro está \' desligado \' .

Os valores suportados para este enum são:

-   **fora:** Desligue qualquer consulta que gere várias tarefas (isto é, abrange vários fragmentos)
-   **depurar:** Registos de declaração no nível de severidade de DEBUG.
-   **Log:** Regista declaração no nível de severidade LOG. A linha de registo incluirá a consulta SQL que foi executada.
-   **Aviso:** Regista declaração no nível de severidade DO AVISO.
-   **Aviso:** Registos de declaração ao nível de severidade de ADVERTÊNCIA.
-   **erro:** Regista a declaração no nível de severidade ERROR.

Pode ser útil para ser utilizado durante os `error` testes de desenvolvimento, e um nível de log mais baixo como durante a `log` implantação real da produção.
A escolha `log` fará com que as consultas multi-tarefas apareçam nos registos de base de dados com a consulta em si mostrada após a \" DECLARAÇÃO.\"

```
LOG:  multi-task query about to be executed
HINT:  Queries are split to multiple tasks if they have to be split into several queries on the workers.
STATEMENT:  select * from foo;
```

#### <a name="citusenable_repartition_joins-boolean"></a>citus.permitir \_ a repartição \_ de juntas (boolean)

Normalmente, tentar executar a repartição junta-se ao executor adaptativo falhará com uma mensagem de erro.  No entanto, a definição `citus.enable_repartition_joins` para a verdade permite que a Hyperscale (Citus) mude temporariamente para o executor do rastreador de tarefas para executar a junção.  O valor predefinido é false.

### <a name="task-tracker-executor-configuration"></a>Configuração do executor de rastreador de tarefas

#### <a name="citustask_tracker_delay-integer"></a>citus.task \_ tracker \_ delay (inteiro)

Este parâmetro define o tempo de sono do rastreador de tarefas entre as rondas de gestão de tarefas e os predefinidos a 200 ms. O processo de rastreador de tarefas acorda regularmente, percorre todas as tarefas que lhe são atribuídas e programa e executa essas tarefas.  Em seguida, o rastreador de tarefas dorme por um período de tempo antes de passar por cima destas tarefas novamente.
Este valor de configuração determina o comprimento desse período de sono. Este parâmetro é eficaz nos trabalhadores e precisa de ser alterado no ficheiro postgresql.conf. Após a edição do ficheiro config, os utilizadores podem enviar um sinal SIGHUP ou reiniciar o servidor para que a alteração entre em vigor.

Este parâmetro pode ser diminuído para atenuar o atraso causado devido ao executor de rastreador de tarefas, reduzindo o intervalo de tempo entre as rondas de gestão.
Diminuir o atraso é útil nos casos em que as consultas de fragmentos são curtas e, portanto, atualizar o seu estado regularmente.

#### <a name="citusmax_assign_task_batch_size-integer"></a>citus.max \_ atribuir tamanho do lote de tarefa \_ \_ \_ (inteiro)

O executor de rastreador de tarefas do coordenador atribui sincronizadamente tarefas em lotes ao daemon sobre os trabalhadores. Este parâmetro define o número máximo de tarefas a atribuir num único lote. Escolher um tamanho de lote maior permite uma tarefa mais rápida. No entanto, se o número de trabalhadores for elevado, então pode demorar mais tempo para que todos os trabalhadores obtenham tarefas.  Este parâmetro pode ser definido em tempo de execução e é eficaz no coordenador.

#### <a name="citusmax_running_tasks_per_node-integer"></a>citus.max \_ tarefas de execução \_ por nó \_ \_ (inteiro)

O processo de rastreio de tarefas programa e executa as tarefas que lhe são atribuídas conforme apropriado. Este valor de configuração define o número máximo de tarefas a executar simultaneamente num nó em qualquer momento e predefinições a 8.

O limite garante que \' não tem muitas tarefas a bater no disco ao mesmo tempo, e ajuda a evitar a contenção de E/S do disco. Se as suas consultas forem servidas de memória ou SSDs, pode aumentar as tarefas de funcionamento máximo \_ por nó sem grande \_ \_ \_ preocupação.

#### <a name="cituspartition_buffer_size-integer"></a>tamanho do tampão citus.partition \_ \_ (inteiro)

Define o tamanho do tampão para utilizar para operações de partição e predefinições para 8 MB.
A hiperescala (Citus) permite que os dados da tabela sejam repartidos em vários ficheiros quando duas grandes tabelas estão a ser unidas. Depois de este tampão de partição se encher, os dados repartidos são colocados em ficheiros no disco.  Esta entrada de configuração pode ser definida no tempo de execução e é eficaz nos trabalhadores.

### <a name="explain-output"></a>Explicar saída

#### <a name="citusexplain_all_tasks-boolean"></a>citus.explicar \_ todas as \_ tarefas (boolean)

Por predefinição, a Hyperscale (Citus) mostra a saída de uma única tarefa arbitrária ao executar [EXPLICAção](http://www.postgresql.org/docs/current/static/sql-explain.html) numa consulta distribuída. Na maioria dos casos, a saída explicada será semelhante em todas as tarefas. Ocasionalmente, algumas das tarefas serão planeadas de forma diferente ou terão tempos de execução muito mais elevados. Nesses casos, pode ser útil ativar este parâmetro, após o qual a saída EXPLAIN incluirá todas as tarefas. Explicar todas as tarefas pode levar o EXPLAIN a demorar mais tempo.

## <a name="next-steps"></a>Próximos passos

* Outra forma de configuração, além dos parâmetros do servidor, são as opções de [configuração](concepts-hyperscale-configuration-options.md) de recursos num grupo de servidores Hyperscale (Citus).
* A base de dados postgreSQL subjacente também tem [parâmetros de configuração.](http://www.postgresql.org/docs/current/static/runtime-config.html)
