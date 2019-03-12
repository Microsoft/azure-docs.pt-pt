---
title: Store de consulta na base de dados do Azure para PostgreSQL
description: Este artigo descreve a funcionalidade de Store de consulta na base de dados do Azure para PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 8cd4cf12390ff29754b55d2827ea4750e7123a27
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57730493"
---
# <a name="monitor-performance-with-the-query-store"></a>Monitorizar o desempenho com o Store de consulta

**Aplica-se a:** Base de dados do Azure para PostgreSQL 9.6 e 10

> [!IMPORTANT]
> A funcionalidade de consulta Store está em pré-visualização pública.


A funcionalidade de Store de consulta no banco de dados do Azure para PostgreSQL fornece uma maneira de controlar o desempenho das consultas ao longo do tempo. Consulta Store simplifica o desempenho de resolução de problemas, ajudando-o rapidamente encontrar as consultas de maior duração e muitos mais recursos. Consulta Store automaticamente captura um histórico das consultas e estatísticas de tempo de execução e mantém-los para revisão. Ela separa dados por intervalos de tempo para que possa ver padrões de utilização de bases de dados. Os dados para todos os utilizadores, as bases de dados e consultas são armazenados numa base de dados com o nome **azure_sys** na base de dados do Azure para PostgreSQL a instância.

> [!IMPORTANT]
> Não modifique os **azure_sys** base de dados ou seus esquemas. Se o fizer, irá impedir Store de consulta e funcionalidades de desempenho relacionados a funcionar corretamente.

## <a name="enabling-query-store"></a>Ativar consulta Store
Consulta Store é um recurso opcional, para que ele não estará ativo por predefinição num servidor. O arquivo está ativado ou desativado globalmente para todas as bases de dados num determinado servidor e não pode ser ativado ou desativado por base de dados.

### <a name="enable-query-store-using-the-azure-portal"></a>Ativar Store de consulta com o portal do Azure
1. Inicie sessão no portal do Azure e selecione a sua base de dados do Azure para o servidor PostgreSQL.
2. Selecione **parâmetros do servidor** no **definições** secção do menu.
3. Procure o `pg_qs.query_capture_mode` parâmetro.
4. Defina o valor como `TOP` e **guardar**.

Para ativar as estatísticas de espera no seu Store de consulta: 
5. Procure o `pgms_wait_sampling.query_capture_mode` parâmetro.
6. Defina o valor como `ALL` e **guardar**.


Em alternativa pode definir estes parâmetros com a CLI do Azure.
```azurecli-interactive
az postgres server configuration set --name pg_qs.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value TOP
az postgres server configuration set --name pgms_wait_sampling.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value ALL
```

Permitir que até 20 minutos para que o primeiro grupo de dados para se manter na base de dados do azure_sys.

## <a name="information-in-query-store"></a>Informações em Store de consulta
Consulta Store tem dois arquivos:
- Um arquivo de estatísticas de tempo de execução para manter as informações de estatísticas de execução de consulta.
- Um arquivo de estatísticas de espera para manter informações de estatísticas de espera.

Cenários comuns para utilizar o Query Store incluem:
- Determinar o número de vezes que uma consulta foi executada numa janela de tempo especificado
- Comparar o tempo médio de execução de uma consulta em intervalos de tempo para ver os deltas grandes
- Identificar as consultas de execução mais longa no passado X horas
- Identificação de consultas de N principais que estão a aguardar nos recursos
- Natureza de espera de compreensão de uma consulta específica

Para minimizar a utilização de espaço, as estatísticas de execução do tempo de execução no arquivo de estatísticas de tempo de execução são agregadas ao longo de uma janela de tempo fixo e configurável. As informações desses arquivos são visíveis ao consultar os modos de exibição do arquivo de consulta.

A consulta seguinte devolve informações sobre consultas no Query Store:
```sql
SELECT * FROM query_store.qs_view; 
``` 

Ou esta consulta para estatísticas de espera:
```sql
SELECT * FROM query_store.pgms_wait_sampling_view;
```

## <a name="finding-wait-queries"></a>A localizar consultas de espera
Tipos de eventos de espera combinam diferentes de espera eventos em buckets por semelhança. Consulta Store fornece o tipo de evento de espera, nome do evento específico de espera e a consulta em questão. A capacidade de correlacionar essas informações de espera com o tempo de execução de consulta estatísticas significa que pode obter um conhecimento mais aprofundado sobre o que contribui para características de desempenho de consulta.

Aqui estão alguns exemplos de como pode obter mais informações sobre sua carga de trabalho com as estatísticas de espera no Query Store:

| **Observação** | **ação** |
|---|---|
|Esperas de bloqueio elevado | Verifique os textos de consulta para consultas afetadas e identificar as entidades de destino. Procure no Query Store outras consultas modificar a mesma entidade, o que é executada com frequência e/ou tem alta duração. Depois de identificar estas consultas, considere alterar a lógica do aplicativo para aprimorar a simultaneidade ou utilizar um nível de isolamento menos restritivo.|
| Esperas de alto de memória intermédia de e/s | Determinar as consultas com um elevado número de leituras físicas na Store de consulta. Se coincidirem as consultas com alta esperas de e/s, considere a introdução de um índice na entidade subjacente, para fazer buscas em vez de análises. Isso seria minimizar a sobrecarga de e/s das consultas. Verifique os **recomendações de desempenho** para o seu servidor no portal para ver se existem recomendações de índice para este servidor que seria otimizar as consultas.|
| Esperas de memória elevadas | Encontre a consultas no Query Store de consumo de memória superior. Estas consultas são provavelmente atrasando ainda mais o progresso das consultas afetadas. Verifique os **recomendações de desempenho** para o seu servidor no portal para ver se existem recomendações de índice que seriam a otimizar estas consultas.|

## <a name="configuration-options"></a>Opções de configuração
Quando a consulta Store está ativada guarda os dados no windows de agregação de 15 minutos, até 500 consultas distintas por janela. 

As seguintes opções estão disponíveis para configurar parâmetros de consulta Store.
| **Parâmetro** | **Descrição** | **Predefinição** | **Range**|
|---|---|---|---|
| pg_qs.query_capture_mode | Define quais declarações são controladas. | nenhum | Nenhum, principais, tudo |
| pg_qs.max_query_text_length | Define o período de consulta máxima que pode ser salvo. Consultas mais tempo serão truncadas. | 6000 | 100 - 10K |
| pg_qs.retention_period_in_days | Define o período de retenção. | 7 | 1 - 30 |
| pg_qs.track_utility | Define se são controlados comandos de utilitário | em | on, off |

As opções seguintes aplicam-se especificamente a estatísticas de espera.
| **Parâmetro** | **Descrição** | **Predefinição** | **Range**|
|---|---|---|---|
| pgms_wait_sampling.query_capture_mode | Conjuntos de declarações são controladas por estatísticas de espera. | nenhum | NONE, tudo|
| Pgms_wait_sampling.history_period | Defina a frequência, em milissegundos, no qual espera os eventos são recolhidos. | 100 | 1-600000 |

> [!NOTE] 
> **pg_qs.query_capture_mode** substitui **pgms_wait_sampling.query_capture_mode**. Se pg_qs.query_capture_mode for NONE, a definição de pgms_wait_sampling.query_capture_mode não tem efeito.


Utilize o [portal do Azure](howto-configure-server-parameters-using-portal.md) ou [CLI do Azure](howto-configure-server-parameters-using-cli.md) para obter ou definir um valor diferente para um parâmetro.

## <a name="views-and-functions"></a>Funções e exibições
Ver e gerir Store de consulta com as seguintes vistas e funções. Qualquer pessoa na função pública PostgreSQL pode utilizar estas vistas para ver os dados na consulta Store. Estas vistas só estão disponíveis na **azure_sys** base de dados.

Consultas são normalizadas examinando sua estrutura após a remoção de literais e constantes. Se duas consultas são idênticas, exceto para valores literais, eles terão o mesmo hash.

### <a name="querystoreqsview"></a>query_store.qs_view
Esta vista devolve todos os dados na consulta Store. Há uma linha para cada base de dados distinto ID, o ID de utilizador e o ID de consulta. 

|**Nome**   |**Tipo** | **Referências**  | **Descrição**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | ID da tabela runtime_stats_entries|
|user_id    |OID    |pg_authid.oid  |OID de utilizador que executou a instrução|
|db_id  |OID    |pg_database.oid    |OID da base de dados em que a instrução foi executada|
|query_id   |bigint  || Código de hash interna, calculado a partir da árvore de análise a instrução|
|query_sql_text |Varchar(10000)  || Texto de uma instrução representativa. Diferentes consultas com a mesma estrutura sejam agrupadas; Este texto é o texto para a primeira das consultas no cluster.|
|plan_id    |bigint |   |ID do plano correspondente a esta consulta não está disponível ainda|
|start_time |carimbo de data/hora  ||  Consultas são agregadas por buckets de tempo - o intervalo de tempo de um registo é de 15 minutos por predefinição. Esta é a hora de início correspondente para o registo de tempo para esta entrada.|
|end_time   |carimbo de data/hora  ||  Hora de fim correspondente para o registo de tempo para esta entrada.|
|chamadas  |bigint  || Número de vezes que a consulta executada|
|total_time |precisão dupla   ||  Tempo de execução total da consulta, em milissegundos|
|min_time   |precisão dupla   ||  Tempo de execução da consulta mínimo, em milissegundos|
|max_time   |precisão dupla   ||  Tempo de execução máxima de consulta, em milissegundos|
|mean_time  |precisão dupla   ||  Significa que o tempo de execução da consulta, em milissegundos|
|stddev_time|   precisão dupla    ||  Desvio-padrão do tempo de execução de consulta, em milissegundos |
|linhas   |bigint ||  Número total de linhas obtido ou afetada pela instrução|
|shared_blks_hit|   bigint  ||  Número total de acertos na cache de blocos partilhado pela instrução|
|shared_blks_read|  bigint  ||  Número total de blocos partilhados pela instrução de leitura|
|shared_blks_dirtied|   bigint   || Número total de blocos partilhados dirtied pela instrução |
|shared_blks_written|   bigint  ||  Número total de blocos partilhados escrito pela instrução|
|local_blks_hit|    bigint ||   Número total de acertos na cache do bloco local pela instrução|
|local_blks_read|   bigint   || Número total de blocos locais pela instrução de leitura|
|local_blks_dirtied|    bigint  ||  Número total de blocos locais dirtied pela instrução|
|local_blks_written|    bigint  ||  Número total de blocos locais escrito pela instrução|
|temp_blks_read |bigint  || Número total de blocos temporários ler pela instrução|
|temp_blks_written| bigint   || Número total de blocos temporários escrito pela instrução|
|blk_read_time  |precisão dupla    || Total de tempo a instrução gasto blocos de leitura, em milissegundos (se track_io_timing estiver ativada, caso contrário, zero)|
|blk_write_time |precisão dupla    || Total de tempo a instrução gasto blocos de escrita, em milissegundos (se track_io_timing estiver ativada, caso contrário, zero)|
    
### <a name="querystorequerytextsview"></a>query_store.query_texts_view
Esta vista devolve os dados de texto de consulta no Query Store. Há uma linha para cada query_text distintos.

|**Nome**|  **Tipo**|   **Descrição**|
|---|---|---|
|query_text_id  |bigint     |ID da tabela de query_texts|
|query_sql_text |Varchar(10000)     |Texto de uma instrução representativa. Diferentes consultas com a mesma estrutura sejam agrupadas; Este texto é o texto para a primeira das consultas no cluster.|

### <a name="querystorepgmswaitsamplingview"></a>query_store.pgms_wait_sampling_view
Esta vista devolve os dados de eventos na consulta Store de espera. Há uma linha para cada ID de base de dados distintos, ID de utilizador, ID de consulta e eventos.

|**Nome**|  **Tipo**|   **Referências**| **Descrição**|
|---|---|---|---|
|user_id    |OID    |pg_authid.oid  |OID de utilizador que executou a instrução|
|db_id  |OID    |pg_database.oid    |OID da base de dados em que a instrução foi executada|
|query_id   |bigint     ||Código de hash interna, calculado a partir da árvore de análise a instrução|
|event_type |texto       ||O tipo de evento para o qual está aguardando o back-end|
|event  |texto       ||O nome do evento espera se back-end está atualmente a aguardar|
|chamadas  |Número Inteiro        ||Número do mesmo evento capturado|


### <a name="functions"></a>Funções
Query_store.qs_reset() returns void

`qs_reset` Elimina todas as estatísticas recolhidas até ao momento pelas Store de consulta. Esta função só pode ser executada pela função de administrador de servidor.

Query_store.staging_data_reset() retorna void

`staging_data_reset` Elimina todas as estatísticas recolhidas na memória pelas Query Store (ou seja, os dados na memória que não foram descarregada, mas para a base de dados). Esta função só pode ser executada pela função de administrador de servidor.

## <a name="limitations-and-known-issues"></a>Limitações e problemas conhecidos
- Se a um servidor PostgreSQL tiver o parâmetro default_transaction_read_only, Store de consulta não é possível capturar os dados.
- Funcionalidade de consulta Store pode ser interrompida se encontrar consultas de Unicode longas (> = 6000 bytes).


## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [cenários em que a consulta Store pode ser especialmente útil](concepts-query-store-scenarios.md).
- Saiba mais sobre [melhores práticas para utilizar o Query Store](concepts-query-store-best-practices.md).
