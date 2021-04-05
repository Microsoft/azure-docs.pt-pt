---
title: Parâmetros do servidor - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Parâmetros na Hiperescala (Citus) SQL API
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 07f966c7b0be542f848f1a0a4eaf2b5549735b4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91336246"
---
# <a name="server-parameters"></a>Parâmetros do servidor

Existem vários parâmetros do servidor que afetam o comportamento da Hiperescala (Citus), tanto a partir do PostgreSQL padrão, como específicos de Hyperscale (Citus).
Estes parâmetros podem ser definidos no portal Azure para um grupo de servidores de Hiperescala (Citus). Na categoria **Definições,** escolha **parâmetros de nó do trabalhador** ou **parâmetros do nó coordenador**. Estas páginas permitem definir parâmetros para todos os nós dos trabalhadores, ou apenas para o nó coordenador.

## <a name="hyperscale-citus-parameters"></a>Parâmetros de hiperescala (Citus)

> [!NOTE]
>
> Os grupos de servidores de hiperescala (Citus) que executam versões mais antigas do Citus Engine podem não oferecer todos os parâmetros listados abaixo.

### <a name="general-configuration"></a>Configuração geral

#### <a name="citususe_secondary_nodes-enum"></a>citus.use \_ \_ nós secundários (enum)

Define a política a utilizar ao escolher nós para consultas SELECT. Se estiver definido para "sempre", então o planejador perguntará apenas nós que são marcados como noderole "secundário" em [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table).

Os valores suportados para este enum são:

-   **nunca:** (padrão) Todas as leituras acontecem nos nós primários.
-   **sempre:** As leituras são executadas contra nós secundários e as declarações de inserção/atualização são desativadas.

#### <a name="cituscluster_name-text"></a>nome citus.cluster \_ (texto)

Informa o coordenador do nós que coordena o agrupamento. Uma vez \_ definido o nome do cluster, o planejador irá consultar os nós dos trabalhadores apenas nesse cluster.

#### <a name="citusenable_version_checks-boolean"></a>citus.enable \_ \_ version checks (boolean)

A atualização da versão Hyperscale (Citus) requer um reinício do servidor (para recolher a nova biblioteca partilhada), seguido do comando ALTER EXTENSION UPDATE.  A não execução de ambos os passos pode potencialmente causar erros ou acidentes.
A hiperescala (Citus) valida assim a versão do código e a da correspondência de extensão, e falha se não o \' fizerem.

Este valor não é verdadeiro e é eficaz no coordenador. Em casos raros, processos de atualização complexos podem exigir a definição deste parâmetro como falso, desativando assim a verificação.

#### <a name="cituslog_distributed_deadlock_detection-boolean"></a>citus.log \_ deteção de impasse distribuído \_ \_ (booleano)

Se registar o processamento distribuído de deteção de impasse no registo do servidor. Não é falso.

#### <a name="citusdistributed_deadlock_detection_factor-floating-point"></a>citus.distributed \_ fator de deteção de impasse \_ \_ (ponto flutuante)

Define o tempo para esperar antes de verificar se há impasses distribuídos. Em particular, o tempo de espera será este valor multiplicado pela \' definição de [tempo de \_ tempo](https://www.postgresql.org/docs/current/static/runtime-config-locks.html) de impasse postgreSQL. O valor predefinido é `2`. Um valor de `-1` desativação distribuído deteção de impasse.

#### <a name="citusnode_connection_timeout-integer"></a>tempo limite de ligação citus.node \_ \_ (inteiro)

O `citus.node_connection_timeout` GUC define a duração máxima (em milissegundos) para aguardar o estabelecimento de ligação. A hiperescala (Citus) levanta um erro se o tempo limite decorrer antes de ser estabelecida pelo menos uma ligação de trabalhador. Este GUC afeta as ligações do coordenador aos trabalhadores e dos trabalhadores uns com os outros.

-   Predefinição: cinco segundos
-   Mínimo: 10 milissegundos
-   Máximo: uma hora

```postgresql
-- set to 30 seconds
ALTER DATABASE foo
SET citus.node_connection_timeout = 30000;
```

### <a name="query-statistics"></a>Estatísticas de Consulta

#### <a name="citusstat_statements_purge_interval-integer"></a>citus.stat \_ statements purga intervalo \_ \_ (inteiro)

Define a frequência em que o daemon de manutenção remove registos de [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table) incomparáveis em `pg_stat_statements` . Este valor de configuração define o intervalo de tempo entre purgas em segundos, com um valor predefinido de 10. Um valor de 0 desativa as purgas.

```psql
SET citus.stat_statements_purge_interval TO 5;
```

Este parâmetro é eficaz no coordenador e pode ser alterado no tempo de funcionamento.

### <a name="data-loading"></a>Carregamento de dados

#### <a name="citusmulti_shard_commit_protocol-enum"></a>protocolo de \_ compromisso citus.multi-fragmentos \_ \_ (enum)

Define o protocolo de compromisso a utilizar ao executar COPY numa tabela distribuída de haxixe. Em cada colocação de fragmentos individuais, o COPY é realizado num bloco de transações para garantir que não são ingeridos dados se ocorrer um erro durante o COPY. No entanto, existe um caso particular de falha em que o COPY tem sucesso em todas as colocações, mas uma falha (hardware) ocorre antes de todas as transações se cometerem. Este parâmetro pode ser utilizado para evitar a perda de dados nesse caso, escolhendo entre os seguintes protocolos de compromisso:

-   **2pc:** (padrão) As transações em que copy é realizado nas colocações de fragmentos são preparadas primeiro usando o \' [compromisso de duas fases](http://www.postgresql.org/docs/current/static/sql-prepare-transaction.html) do PostgreSQL e depois cometidos. Os compromissos falhados podem ser recuperados manualmente ou abortados utilizando o COMMIT PREPARED ou o ROLLBACK PREPARADO, respectivamente.
    Ao utilizar 2pc, [ \_ as \_ transações preparadas max](http://www.postgresql.org/docs/current/static/runtime-config-resource.html) devem ser aumentadas em todos os trabalhadores, tipicamente com o mesmo valor que as \_ ligações máximas.
-   **1pc:** As transações em que o COPY é realizado nas colocações de fragmentos são cometidas numa única ronda. Os dados podem ser perdidos se um compromisso falhar após o COPY ter sucesso em todas as colocações (raras).

#### <a name="citusshard_replication_factor-integer"></a>citus.shard \_ \_ replication fator (inteiro)

Define o fator de replicação para fragmentos que é, o número de nós em que os fragmentos serão colocados, e predefinidos para 1. Este parâmetro pode ser definido no tempo de execução e é eficaz no coordenador. O valor ideal para este parâmetro depende do tamanho do cluster e da taxa de falha do nó.  Por exemplo, pode querer aumentar este fator de replicação se executar grandes aglomerados e observar falhas de nó numa base mais frequente.

#### <a name="citusshard_count-integer"></a>citus.shard \_ count (inteiro)

Define a contagem de fragmentos para tabelas de hash-partitioned e predefinições para 32.  Este valor é usado pelo [create_distributed_table](reference-hyperscale-functions.md#create_distributed_table) UDF ao criar mesas de partição de haxixe. Este parâmetro pode ser definido no tempo de execução e é eficaz no coordenador.

#### <a name="citusshard_max_size-integer"></a>citus.shard \_ tamanho máximo \_ (inteiro)

Define o tamanho máximo ao qual um fragmento crescerá antes de ser dividido e predefinido para 1 GB. Quando o tamanho do ficheiro de origem \' (que é utilizado para a encenação) para um fragmento excede este valor de configuração, a base de dados garante que um novo fragmento é criado. Este parâmetro pode ser definido no tempo de execução e é eficaz no coordenador.

### <a name="planner-configuration"></a>Configuração do planejador

#### <a name="cituslimit_clause_row_fetch_count-integer"></a>citus.limit \_ clause row fetch count \_ \_ \_ (inteiro)

Define o número de linhas para obter por tarefa para a otimização da cláusula de limite.
Em alguns casos, consultas selecionadas com cláusulas limite podem precisar de buscar todas as linhas de cada tarefa para gerar resultados. Nesses casos, e onde uma aproximação produziria resultados significativos, este valor de configuração define o número de linhas para obter de cada fragmento. As aproximações de limite são desativadas por predefinição e este parâmetro está definido para -1. Este valor pode ser definido no tempo de execução e é eficaz no coordenador.

#### <a name="cituscount_distinct_error_rate-floating-point"></a>citus.count \_ taxa de erro distinta \_ \_ (ponto flutuante)

A hiperescala (Citus) pode calcular a contagem (distinta) aproximadas usando a extensão pós-hora-hll. Esta entrada de configuração define a taxa de erro desejada ao calcular a contagem (distinta). 0.0, que é o padrão, desativa as aproximações para a contagem (distinta); e 1.0 não fornece garantias sobre a exatidão dos resultados. Recomendamos definir este parâmetro para 0,005 para obter os melhores resultados. Este valor pode ser definido no tempo de execução e é eficaz no coordenador.

#### <a name="citustask_assignment_policy-enum"></a>\_ \_ citus.task assignment policy (enum)

> [!NOTE]
> Este GUC só é aplicável quando [shard_replication_fator](reference-hyperscale-parameters.md#citusshard_replication_factor-integer) for maior do que um, ou para consultas contra [reference_tables](concepts-hyperscale-distributed-data.md#type-2-reference-tables).

Define a política a utilizar na atribuição de tarefas aos trabalhadores. O coordenador atribui tarefas aos trabalhadores com base em locais de shard. Este valor de configuração especifica a política a utilizar ao esemear estas atribuições.
Atualmente, existem três possíveis políticas de atribuição de tarefas que podem ser usadas.

-   **ganancioso:** A política gananciosa é o incumprimento e visa distribuir uniformemente tarefas pelos trabalhadores.
-   **rodada-robin:** A política de rodapé atribui tarefas aos trabalhadores de forma redonda alternando entre réplicas diferentes. Esta política permite uma melhor utilização do cluster quando a contagem de fragmentos para uma tabela é baixa em comparação com o número de trabalhadores.
-   **primeira réplica:** A política de primeira réplica atribui tarefas com base na ordem de inserção de colocações (réplicas) para os fragmentos. Por outras palavras, a consulta de fragmento para um fragmento é atribuída ao trabalhador que tem a primeira réplica desse fragmento.
    Este método permite-lhe ter fortes garantias sobre quais fragmentos serão usados em que nó (isto é, garantias de residência de memória mais fortes).

Este parâmetro pode ser definido no tempo de execução e é eficaz no coordenador.

### <a name="intermediate-data-transfer"></a>Transferência intermédia de dados

#### <a name="citusbinary_worker_copy_format-boolean"></a>formato de cópia do trabalhador citus.binary \_ \_ \_ (boolean)

Utilize o formato de cópia binária para transferir dados intermédios entre os trabalhadores.
Durante a grande mesa, a Hyperscale (Citus) pode ter de repartir dinamicamente e baralhar dados entre diferentes trabalhadores. Por predefinição, estes dados são transferidos em formato de texto. O habilitar este parâmetro instrui a base de dados a utilizar o formato binário de serialização do PostgreSQL para transferir estes dados. Este parâmetro é eficaz nos trabalhadores e precisa de ser alterado no ficheiro postgresql.conf. Após a edição do ficheiro config, os utilizadores podem enviar um sinal SIGHUP ou reiniciar o servidor para que esta alteração entre em vigor.

#### <a name="citusbinary_master_copy_format-boolean"></a>formato de cópia master citus.binary \_ \_ \_ (boolean)

Utilize o formato de cópia binária para transferir dados entre o coordenador e os trabalhadores. Ao executar consultas distribuídas, os trabalhadores transferem os seus resultados intermédios para o coordenador para agregação final. Por predefinição, estes dados são transferidos em formato de texto. O habilitar este parâmetro instrui a base de dados a utilizar o formato binário de serialização do PostgreSQL para transferir estes dados.
Este parâmetro pode ser definido em tempo de execução e é eficaz no coordenador.

#### <a name="citusmax_intermediate_result_size-integer"></a>citus.max tamanho \_ do resultado intermédio \_ \_ (inteiro)

A dimensão máxima em KB dos resultados intermédios para os CTEs que não podem ser empurrados para baixo para os nós dos trabalhadores para a execução, e para subqueries complexos. O padrão é de 1 GB, e um valor de -1 significa que não há limite.
As consultas que excedam o limite serão canceladas e produzirão uma mensagem de erro.

### <a name="ddl"></a>DDL

#### <a name="citusenable_ddl_propagation-boolean"></a>citus.permitir \_ a propagação ddl \_ (boolean)

Especifica se propagar automaticamente o DDL do coordenador para todos os trabalhadores. O valor predefinido é true. Como algumas alterações de esquema requerem um bloqueio exclusivo de acesso nas mesas, e porque a propagação automática se aplica a todos os trabalhadores sequencialmente, pode tornar um cluster de Hiperescala (Citus) temporariamente menos responsivo. Pode optar por desativar esta definição e propagar alterações manualmente.

### <a name="executor-configuration"></a>Configuração do executor

#### <a name="general"></a>Geral

##### <a name="citusall_modifications_commutative"></a>citus.todas as \_ modificações \_ comutativas

A Hiperescala (Citus) impõe regras de comutação e adquire fechaduras apropriadas para modificar operações de forma a garantir a correção do comportamento. Por exemplo, assume que uma declaração INSERT viaja com outra declaração INSERT, mas não com uma declaração DE ATUALIZAÇÃO ou DELETE. Da mesma forma, assume que uma declaração DE ATUALIZAÇÃO ou DELETE não se desloca com outra declaração DE ATUALIZAÇÃO ou DELETE. Esta precaução significa que UPDATEs e DELETEs exigem que a Hyperscale (Citus) adquira fechaduras mais fortes.

Se tiver declarações DE ATUALIZAÇÃO que sejam comutativas com os seus INSERTs ou outros UPDATEs, então pode relaxar estes pressupostos de comutação definindo este parâmetro para o verdadeiro. Quando este parâmetro é definido como verdadeiro, todos os comandos são considerados comutativos e reivindicam uma fechadura partilhada, o que pode melhorar a produção geral. Este parâmetro pode ser definido em tempo de execução e é eficaz no coordenador.

##### <a name="citusremote_task_check_interval-integer"></a>citus.remote \_ task check interval \_ \_ (inteiro)

Define a frequência em que a Hyperscale (Citus) verifica os estatutos dos postos de trabalho geridos pelo executor de rastreadores de tarefas. Falha em 10 ms. O coordenador atribui tarefas aos trabalhadores e, em seguida, verifica regularmente com eles sobre cada \' trabalho de progresso. Este valor de configuração define o intervalo de tempo entre duas verificações consequentes. Este parâmetro é eficaz no coordenador e pode ser definido em tempo de execução.

##### <a name="citustask_executor_type-enum"></a>citus.task \_ executor \_ (enum)

A Hiperescala (Citus) tem três tipos de executor para executar consultas SELECT distribuídas.  O executor pretendido pode ser selecionado definindo este parâmetro de configuração. Os valores aceites para este parâmetro são:

-   **adaptativo:** O padrão. É ideal para respostas rápidas a consultas que envolvem agregações e juntas de colocated que se estendem por vários fragmentos.
-   **rastreador de tarefas:** O executor de rastreador de tarefas é adequado para consultas complexas e de longa duração que requerem baralhar dados através dos nós dos trabalhadores e uma gestão eficiente dos recursos.
-   **em tempo real:** (precotado) Serve um propósito semelhante ao executor adaptativo, mas é menos flexível e pode causar mais pressão de ligação nos nós dos trabalhadores.

Este parâmetro pode ser definido no tempo de execução e é eficaz no coordenador.

##### <a name="citusmulti_task_query_log_level-enum-multi_task_logging"></a>citus.multi \_ \_ nível de registo de consulta de tarefa \_ \_ (enum) {#multi_task_logging}

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

##### <a name="citusenable_repartition_joins-boolean"></a>citus.permitir \_ a repartição \_ de juntas (boolean)

Normalmente, tentar executar a repartição junta-se ao executor adaptativo falhará com uma mensagem de erro.  No entanto, a definição `citus.enable_repartition_joins` para a verdade permite que a Hyperscale (Citus) mude temporariamente para o executor do rastreador de tarefas para executar a junção.  O valor predefinido é false.

#### <a name="task-tracker-executor-configuration"></a>Configuração do executor de rastreador de tarefas

##### <a name="citustask_tracker_delay-integer"></a>citus.task \_ tracker \_ delay (inteiro)

Este parâmetro define o tempo de sono do rastreador de tarefas entre as rondas de gestão de tarefas e os predefinidos a 200 ms. O processo de rastreador de tarefas acorda regularmente, percorre todas as tarefas que lhe são atribuídas e programa e executa essas tarefas.  Em seguida, o rastreador de tarefas dorme por um período de tempo antes de passar por cima destas tarefas novamente.
Este valor de configuração determina o comprimento desse período de sono. Este parâmetro é eficaz nos trabalhadores e precisa de ser alterado no ficheiro postgresql.conf. Após a edição do ficheiro config, os utilizadores podem enviar um sinal SIGHUP ou reiniciar o servidor para que a alteração entre em vigor.

Este parâmetro pode ser diminuído para atenuar o atraso causado devido ao executor de rastreador de tarefas, reduzindo o intervalo de tempo entre as rondas de gestão.
Diminuir o atraso é útil nos casos em que as consultas de fragmentos são curtas e, portanto, atualizar o seu estado regularmente.

##### <a name="citusmax_assign_task_batch_size-integer"></a>citus.max atribuir \_ \_ o tamanho do lote de tarefa \_ \_ (inteiro)

O executor de rastreador de tarefas do coordenador atribui sincronizadamente tarefas em lotes ao daemon sobre os trabalhadores. Este parâmetro define o número máximo de tarefas a atribuir num único lote. Escolher um tamanho de lote maior permite uma tarefa mais rápida. No entanto, se o número de trabalhadores for elevado, então pode demorar mais tempo para que todos os trabalhadores obtenham tarefas.  Este parâmetro pode ser definido em tempo de execução e é eficaz no coordenador.

##### <a name="citusmax_running_tasks_per_node-integer"></a>citus.max \_ tarefas de execução \_ por nó \_ \_ (inteiro)

O processo de rastreio de tarefas programa e executa as tarefas que lhe são atribuídas conforme apropriado. Este valor de configuração define o número máximo de tarefas a executar simultaneamente num nó em qualquer momento e predefinições a 8.

O limite garante que \' não tem muitas tarefas a bater no disco ao mesmo tempo, e ajuda a evitar a contenção de E/S do disco. Se as suas consultas forem servidas de memória ou SSDs, pode aumentar as tarefas de funcionamento máximo \_ por nó sem grande \_ \_ \_ preocupação.

##### <a name="cituspartition_buffer_size-integer"></a>tamanho do tampão citus.partition \_ \_ (inteiro)

Define o tamanho do tampão para utilizar para operações de partição e predefinições para 8 MB.
A hiperescala (Citus) permite que os dados da tabela sejam repartidos em vários ficheiros quando duas grandes tabelas estão a ser unidas. Depois de este tampão de partição se encher, os dados repartidos são colocados em ficheiros no disco.  Esta entrada de configuração pode ser definida no tempo de execução e é eficaz nos trabalhadores.

#### <a name="explain-output"></a>Explicar saída

##### <a name="citusexplain_all_tasks-boolean"></a>citus.explicar \_ todas as \_ tarefas (boolean)

Por predefinição, a Hyperscale (Citus) mostra a saída de uma única tarefa arbitrária ao executar [EXPLICAção](http://www.postgresql.org/docs/current/static/sql-explain.html) numa consulta distribuída. Na maioria dos casos, a saída explicada será semelhante em todas as tarefas. Ocasionalmente, algumas das tarefas serão planeadas de forma diferente ou terão tempos de execução muito mais elevados. Nesses casos, pode ser útil ativar este parâmetro, após o qual a saída EXPLAIN incluirá todas as tarefas. Explicar todas as tarefas pode levar o EXPLAIN a demorar mais tempo.

## <a name="postgresql-parameters"></a>Parâmetros postgreSQL

* [DateStyle](https://www.postgresql.org/docs/current/datatype-datetime.html#DATATYPE-DATETIME-OUTPUT) - Define o formato de exibição para valores de data e hora
* [IntervaloStyle](https://www.postgresql.org/docs/current/datatype-datetime.html#DATATYPE-INTERVAL-OUTPUT) - Define o formato de exibição para valores de intervalo
* [TimeZone](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-TIMEZONE) - Define o fuso horário para visualização e interpretação de selos temporais
* [application_name](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-APPLICATION-NAME) - Define o nome da aplicação a ser reportado em estatísticas e registos
* [array_nulls](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-ARRAY-NULLS) - Permite a entrada de elementos NULOS em matrizes
* [autovacuum](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM) - Inicia o subprocesso autovacuum
* [autovacuum_analyze_scale_fator](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-ANALYZE-SCALE-FACTOR) - Número de inserções de tuple, atualizações ou eliminações antes de analisar como uma fração de reltuples
* [autovacuum_analyze_threshold](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-ANALYZE-THRESHOLD) - Número mínimo de inserções, atualizações ou eliminações antes de analisar
* [autovacuum_naptime](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-NAPTIME) - Hora de dormir entre corridas de autovacuum
* [autovacuum_vacuum_cost_delay](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-DELAY) - Atraso dos custos de vácuo em milissegundos, para autovacuum
* [autovacuum_vacuum_cost_limit](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-LIMIT) - Valor do custo de vácuo disponível antes da sesta, para autovacuum
* [autovacuum_vacuum_scale_fator](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-SCALE-FACTOR) - Número de atualizações ou eliminações antes do vácuo como uma fração de reltuples
* [autovacuum_vacuum_threshold](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-THRESHOLD) - Número mínimo de atualizações ou eliminações de tuples antes do vácuo
* [autovacuum_work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-AUTOVACUUM-WORK-MEM) - Define a memória máxima a ser utilizada por cada processo de trabalhador autovacuum
* [backend_flush_after](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BACKEND-FLUSH-AFTER) - Número de páginas após as quais as escritas anteriormente realizadas são lavadas para o disco
* [backslash_quote](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-BACKSLASH-QUOTE) - Define se \' " " é permitido em cadeias literais
* [bgwriter_delay](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-DELAY) - Tempo de sono do escritor de fundo entre as rondas
* [bgwriter_flush_after](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-FLUSH-AFTER) - Número de páginas após as quais as escritas anteriormente realizadas são lavadas para o disco
* [bgwriter_lru_maxpages](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-LRU-MAXPAGES) - Número máximo de páginas LRU para flush por rodada
* [bgwriter_lru_multiplier](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-LRU-MULTIPLIER) - Múltipla do uso médio do tampão para livre por rodada
* [bytea_output](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-BYTEA-OUTPUT) - Define o formato de saída para bytea
* [check_function_bodies](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CHECK-FUNCTION-BODIES) - Verifica os órgãos de função durante a função CREATE
* [checkpoint_completion_target](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-COMPLETION-TARGET) - Tempo gasto a lavar tampão sujo durante o checkpoint, como fração do intervalo de ponto de verificação
* [checkpoint_timeout](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-TIMEOUT) - Define o tempo máximo entre os postos de controlo automáticos WAL
* [checkpoint_warning](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-WARNING) - Permite avisos se os segmentos de checkpoint são preenchidos com mais frequência do que este
* [client_encoding](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CLIENT-ENCODING) - Define o conjunto de caracteres do cliente codificando
* [client_min_messages](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CLIENT-MIN-MESSAGES) - Define os níveis de mensagem que são enviados ao cliente
* [commit_delay](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-COMMIT-DELAY) - Define o atraso nos microsegundos entre o compromisso de transação e a descarga de WAL para o disco
* [commit_siblings](https://www.postgresql.org/docs/12/runtime-config-wal.html#GUC-COMMIT-SIBLINGS) - Define as transações abertas em simultâneo mínimas antes de efe commit_delay
* [constraint_exclusion](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CONSTRAINT-EXCLUSION) - Permite ao planejador usar constrangimentos para otimizar consultas
* [cpu_index_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-INDEX-TUPLE-COST) - Define a estimativa do planejador do custo de processamento de cada entrada de índice durante uma verificação de índice
* [cpu_operator_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-OPERATOR-COST) - Define a estimativa do planejador do custo de processamento de cada operador ou chamada de função
* [cpu_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-TUPLE-COST) - Define a estimativa do planejador do custo de processamento de cada tuple (linha)
* [cursor_tuple_fraction](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CURSOR-TUPLE-FRACTION) - Define a estimativa do planejador da fração das linhas de um cursor que serão recuperadas
* [deadlock_timeout](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-DEADLOCK-TIMEOUT) - Define a quantidade de tempo, em milissegundos, para esperar numa fechadura antes de verificar se há impasse
* [debug_pretty_print](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.3.1.3) - Parse de indents e mostras de árvores de plano
* [debug_print_parse](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) - Regista a árvore de parse de cada consulta
* [debug_print_plan](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) - Regista o plano de execução de cada consulta
* [debug_print_rewritten](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) - Regista a árvore de parse reescrita de cada consulta
* [default_statistics_target](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-DEFAULT-STATISTICS-TARGET) - Define o objetivo das estatísticas padrão
* [default_tablespace](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TABLESPACE) - Define o espaço de mesa predefinido para criar tabelas e índices em
* [default_text_search_config](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TEXT-SEARCH-CONFIG) - Define a configuração de pesquisa de texto predefinido
* [default_transaction_deferrable](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-DEFERRABLE) - Define o estado de diferir padrão das novas transações
* [default_transaction_isolation](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-ISOLATION) - Define o nível de isolamento de transações de cada nova transação
* [default_transaction_read_only](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-READ-ONLY) - Define o estado de leitura padrão apenas de novas transações
* default_with_oids - Cria novas tabelas com OIDs por padrão
* [effective_cache_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-EFFECTIVE-CACHE-SIZE) - Define a suposição do planejador sobre o tamanho da cache do disco
* [enable_bitmapscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-BITMAPSCAN) - Permite o uso do planejador de planos de digitalização de bitmap
* [enable_gathermerge](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-GATHERMERGE) - Permite o uso do planejador de planos de fusão
* [enable_hashagg](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-HASHAGG) - Permite o uso do planejador de planos de agregação de haxixe
* [enable_hashjoin](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-HASHJOIN) - Permite o uso do planejador de planos de junção de haxixe
* [enable_indexonlyscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-INDEXONLYSCAN) - Permite a utilização do planejador de planos de digitalização apenas de índice
* [enable_indexscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-INDEXSCAN) - Permite a utilização do planejador de planos de verificação de índices
* [enable_material](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-MATERIAL) - Permite o uso da materialização do planejador
* [enable_mergejoin](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-MERGEJOIN) - Permite o uso do planejador de planos de fusão
* [enable_nestloop](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-NESTLOOP) - Permite o uso do planejador de planos de junção aninhado
* [enable_seqscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-SEQSCAN) - Permite a utilização do planejador de planos de digitalização sequencial
* [enable_sort](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-SORT) - Permite o uso do planejador de passos de classificação explícito
* [enable_tidscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-TIDSCAN) - Permite a utilização do planejador de planos de digitalização de TID
* [escape_string_warning](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-ESCAPE-STRING-WARNING) - Adverte sobre fugas de costas em literais de cordas comuns
* [exit_on_error](https://www.postgresql.org/docs/current/runtime-config-error-handling.html#GUC-EXIT-ON-ERROR) - Termina sessão sobre qualquer erro
* [extra_float_digits](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-EXTRA-FLOAT-DIGITS) - Define o número de dígitos apresentados para valores de ponto flutuante
* [force_parallel_mode](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-FORCE-PARALLEL-MODE) - Utilização de forças de instalações de consulta paralela
* [from_collapse_limit](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-FROM-COLLAPSE-LIMIT) - Define o tamanho da lista FROM para além do qual os subqueries não são colapsados
* [geqo](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO) - Permite a otimização de consultas genéticas
* [geqo_effort](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-EFFORT) - GEQO: o esforço é usado para definir o padrão para outros parâmetros GEQO
* [geqo_generations](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-GENERATIONS) - GEQO: número de iterações do algoritmo
* [geqo_pool_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-POOL-SIZE) - GEQO: número de indivíduos na população
* [geqo_seed](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-SEED) - GEQO: sementes para seleção aleatória de caminhos
* [geqo_selection_bias](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-SELECTION-BIAS) - GEQO: pressão seletiva dentro da população
* [geqo_threshold](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-THRESHOLD) - Define o limiar de itens FROM para além dos quais o GEQO é utilizado
* [gin_fuzzy_search_limit](https://www.postgresql.org/docs/current/runtime-config-client.html#id-1.6.6.14.5.2.2.1.3) - Define o resultado máximo permitido para pesquisa exata por GIN
* [gin_pending_list_limit](https://www.postgresql.org/docs/current/runtime-config-client.html#id-1.6.6.14.2.2.23.1.3) - Define o tamanho máximo da lista pendente para índice de GIN
* [idle_in_transaction_session_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-IDLE-IN-TRANSACTION-SESSION-TIMEOUT) - Define a duração máxima permitida de qualquer transação em atraso
* [join_collapse_limit](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-JOIN-COLLAPSE-LIMIT) - Define o tamanho da lista FROM para além da qual as construções de JOIN não são achatadas
* [lc_monetary](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-MONETARY) - Define o local para formatação de montantes monetários
* [lc_numeric](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-NUMERIC) - Define o local para os números de formatação
* [lo_compat_privileges](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-LO-COMPAT-PRIVILEGES) - Permite o modo de retrocompatibilidade para verificação de privilégios em objetos grandes
* [lock_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LOCK-TIMEOUT) - Define a duração máxima permitida (em milissegundos) de qualquer espera por uma fechadura. 0 desa fica desligado
* [log_autovacuum_min_duration](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#) - Define o tempo mínimo de execução acima do qual as ações de autovacuum serão registadas
* [log_checkpoints](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-CHECKPOINTS) - Regista cada ponto de verificação
* [log_connections](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-CONNECTIONS) - Regista cada ligação bem sucedida
* [log_destination](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DESTINATION) - Define o destino para a saída de registo de servidor
* [log_disconnections](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DISCONNECTIONS) - Registos terminam uma sessão, incluindo a duração
* [log_duration](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DURATION) - Regista a duração de cada declaração SQL concluída
* [log_error_verbosity](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-ERROR-VERBOSITY) - Define a verbosidade das mensagens registadas
* [log_lock_waits](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LOCK-WAITS) - Logs longas esperas
* [log_min_duration_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-DURATION-STATEMENT) - Define o tempo mínimo de execução (em milissegundos) acima do qual as declarações serão registadas. -1 desativa durações da declaração de registo
* [log_min_error_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-ERROR-STATEMENT) - Faz com que todas as declarações que geram erros a este nível ou acima deste nível sejam registadas
* [log_min_messages](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-MESSAGES) - Define os níveis de mensagem que são registados
* [log_replication_commands](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-REPLICATION-COMMANDS) - Regista cada comando de replicação
* [log_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-STATEMENT) - Define o tipo de declarações registadas
* [log_statement_stats](https://www.postgresql.org/docs/current/runtime-config-statistics.html#id-1.6.6.12.3.2.1.1.3) - Para cada consulta, escreve estatísticas de desempenho cumulativas no registo do servidor
* [log_temp_files](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-TEMP-FILES) - Regista a utilização de ficheiros temporários maiores do que este número de quilobytes
* [maintenance_work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM) - Define a memória máxima a ser utilizada para operações de manutenção
* [max_parallel_workers](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PARALLEL-WORKERS) - Define o número máximo de trabalhadores paralelos do que pode estar ativo de uma só vez
* [max_parallel_workers_per_gather](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PARALLEL-WORKERS-PER-GATHER) - Define o número máximo de processos paralelos por nó do executor
* [max_pred_locks_per_page](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-MAX-PRED-LOCKS-PER-PAGE) - Define o número máximo de tuples predicados por página
* [max_pred_locks_per_relation](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-MAX-PRED-LOCKS-PER-RELATION) - Define o número máximo de páginas e tuples pré-fechados por relação
* [max_standby_archive_delay](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-STANDBY-ARCHIVE-DELAY) - Define o atraso máximo antes de cancelar consultas quando um servidor de standby quente está a processar dados DE WAL arquivados
* [max_standby_streaming_delay](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-STANDBY-STREAMING-DELAY) - Define o atraso máximo antes de cancelar consultas quando um servidor de standby quente está a processar dados WAL transmitidos
* [max_sync_workers_per_subscription](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-SYNC-WORKERS-PER-SUBSCRIPTION) - Número máximo de trabalhadores de sincronização de tabelas por subscrição
* [max_wal_size](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-MAX-WAL-SIZE) - Define o tamanho WAL que desencadeia um posto de controlo
* [min_parallel_index_scan_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-MIN-PARALLEL-INDEX-SCAN-SIZE) - Define a quantidade mínima de dados de índice para uma varredura paralela
* [min_wal_size](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-MIN-WAL-SIZE) - Define o tamanho mínimo para encolher o WAL para
* [operator_precedence_warning](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-OPERATOR-PRECEDENCE-WARNING) - Emite um aviso para construções que mudaram de significado desde PostgreSQL 9.4
* [parallel_setup_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-PARALLEL-SETUP-COST) - Define a estimativa do planejador do custo de iniciar processos de trabalhadores para consultas paralelas
* [parallel_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-PARALLEL-TUPLE-COST) - Define a estimativa do planejador do custo de passar cada tuple (linha) de trabalhador para mestre backend
* [pg_stat_statements.save](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) - Economiza estatísticas de pg_stat_statements através de paragens de servidores
* [pg_stat_statements.track](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) - Selecione quais as declarações que são rastreadas por pg_stat_statements
* [pg_stat_statements.track_utility](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) - Selecione se os comandos de utilidade são rastreados por pg_stat_statements
* [quote_all_identifiers](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-QUOTE-ALL-IDENTIFIERS) - Ao gerar fragmentos SQL, cita todos os identificadores
* [random_page_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-RANDOM-PAGE-COST) - Define a estimativa do planejador do custo de uma página de disco não-sequencialmente recolhida
* [row_security](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-ROW-SECURITY) - Permite a segurança da fila
* [search_path](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SEARCH-PATH) - Define a ordem de pesquisa de esquemas para nomes que não são qualificados por esquemas
* [seq_page_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-SEQ-PAGE-COST) - Define a estimativa do planejador do custo de uma página de disco sequencialmente recolhida
* [session_replication_role](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SESSION-REPLICATION-ROLE) - Define o comportamento da sessão para desencadear e reescrever regras
* [standard_conforming_strings](https://www.postgresql.org/docs/current/runtime-config-compatible.html#id-1.6.6.16.2.2.7.1.3) - Causas '...' cordas para tratar as costas literalmente
* [statement_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-STATEMENT-TIMEOUT) - Define a duração máxima permitida (em milissegundos) de qualquer declaração. 0 desa fica desligado
* [synchronize_seqscans](https://www.postgresql.org/docs/current/runtime-config-compatible.html#id-1.6.6.16.2.2.8.1.3) - Permite análises sincronizadas apreendidas
* [synchronous_commit](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-SYNCHRONOUS-COMMIT) - Define o nível de sincronização da transação atual
* [tcp_keepalives_count](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-COUNT) - Número máximo de retransmites de retransmissãos de mantevenivas TCP
* [tcp_keepalives_idle](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-IDLE) - Tempo entre a emissão de keepalives TCP
* [tcp_keepalives_interval](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-INTERVAL) - Tempo entre retransmites de manter a TCP
* [temp_buffers](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-TEMP-BUFFERS) - Define o número máximo de tampão temporário utilizado por cada sessão de base de dados
* [temp_tablespaces](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-TEMP-TABLESPACES) - Define o espaço de mesa para usar para tabelas temporárias e classificar ficheiros
* [track_activities](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-ACTIVITIES) - Recolhe informações sobre a execução de comandos
* [track_counts](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-COUNTS) - Recolhe estatísticas sobre a atividade da base de dados
* [track_functions](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-FUNCTIONS) - Recolhe estatísticas de nível de função sobre a atividade da base de dados
* [track_io_timing](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-IO-TIMING) - Recolha estatísticas de cronometragem para a atividade de base de dados de E/S
* [transform_null_equals](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-TRANSFORM-NULL-EQUALS) - Trata "expr=NULL" como "expr IS NULL"
* [vacuum_cost_delay](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-DELAY) - Atraso nos custos de vácuo em milissegundos
* [vacuum_cost_limit](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-LIMIT) - Valor do custo de vácuo disponível antes da sesta
* [vacuum_cost_page_dirty](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-DIRTY) - Custo de vácuo para uma página sujada por vácuo
* [vacuum_cost_page_hit](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-HIT) - Custo de vácuo para uma página encontrada na cache tampão
* [vacuum_cost_page_miss](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-MISS) - Custo de vácuo para uma página não encontrada na cache tampão
* [vacuum_defer_cleanup_age](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-VACUUM-DEFER-CLEANUP-AGE) - Número de transações através das quais a limpeza VACUUM e HOT deve ser adiada, se houver
* [vacuum_freeze_min_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-FREEZE-MIN-AGE) - Idade mínima em que o ASPIRA DEVE congelar uma fila de mesa
* [vacuum_freeze_table_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-FREEZE-TABLE-AGE) - Idade em que o VACUUM deve digitalizar a mesa inteira para congelar tuples
* [vacuum_multixact_freeze_min_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-MULTIXACT-FREEZE-MIN-AGE) - Idade mínima em que o VACUUM deve congelar um MultiXactId numa fila de mesa
* [vacuum_multixact_freeze_table_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-MULTIXACT-FREEZE-TABLE-AGE) - Idade multixact em que o VACUUM deve digitalizar toda a mesa para congelar tuples
* [wal_receiver_status_interval](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-WAL-RECEIVER-STATUS-INTERVAL) - Define o intervalo máximo entre os relatórios de estado do recetor WAL para o primário
* [wal_writer_delay](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-WAL-WRITER-DELAY) - Tempo entre as descargas wal realizadas no escritor WAL
* [wal_writer_flush_after](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-WAL-WRITER-FLUSH-AFTER) - Quantidade de WAL escrita pelo escritor WAL que desencadeia um flush
* [work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-WORK-MEM) - Define a quantidade de memória a utilizar por operações internas de classificação e tabelas de haxixe antes de escrever para ficheiros de disco temporários
* [xmlbinário](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-XMLBINARY) - Define como os valores binários devem ser codificados em XML
* [xmloption](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-XMLOPTION) - Define se os dados de XML em operações implícitas de análise e serialização devem ser considerados como documentos ou fragmentos de conteúdo

## <a name="next-steps"></a>Passos seguintes

* Outra forma de configuração, além dos parâmetros do servidor, são as opções de [configuração](concepts-hyperscale-configuration-options.md) de recursos num grupo de servidores Hyperscale (Citus).
* A base de dados postgreSQL subjacente também tem [parâmetros de configuração.](http://www.postgresql.org/docs/current/static/runtime-config.html)
